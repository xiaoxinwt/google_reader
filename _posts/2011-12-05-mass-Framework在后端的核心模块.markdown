---
layout: post
title:  "mass Framework在后端的核心模块"
date:   2011-12-05 09:49:00
author: 司徒正美
categories: program
---

## mass Framework在后端的核心模块
### by 司徒正美
### at 2011-12-05 09:49:00
### original <http://www.cnblogs.com/rubylouvre/archive/2011/12/05/2276390.html>

<p>mass Framework前后通吃，后端是基于node.js组建起来。后端与前端不一样，是依赖于node.js内置模块提供的一些API，因此前端那些依赖于浏览器提供的DOM API的模块无法复用。现在掐指一算，基本上核心模块上只有lang, flow能用得上，more则多一点，如random, pinyin, base64, uuid...</p>

<p>node.js内置了模块加载机制，这与我在前端使用的API非常不一致，我就再把它包装一层。核心模块与前端的核心模块在其他方法也尽量保持一致，当然像domReady在后端就不需要，去掉。如类型判定，由于是V8引擎，支持ECMA262v5，因此可以写得精简。最大的不同是，DEBUG用到mass.log，它的第二个参数是表示格式化高亮。</p>

<p>代码是暂时丢在这里，以后还要做各种扩展的！</p>
<pre>(function(){
    //后端部分　2011.12.4 by 司徒正美
    function mass(){}
    var
    version = 0.1,
    class2type = {
        &quot;[object global]&quot; : &quot;Global&quot; ,
        &quot;null&quot; : &quot;Null&quot; ,
        &quot;NaN&quot;  : &quot;NaN&quot;  ,
        &quot;undefined&quot; : &quot;Undefined&quot;
    },
    toString = class2type.toString;
    /**
     * 糅杂，为一个对象添加更多成员
     * @param {Object} target 目标对象
     * @param {Object} source 属性包
     * @return {Object} 目标对象
     */
    function mix(target, source){
        var args = [].slice.call(arguments), key,
        ride = typeof args[args.length - 1] == &quot;boolean&quot; ? args.pop() : true;
        target = target || {};
        for(var i = 1; source = args[i++];){
            for (key in source) {
                if (ride || !(key in target)) {
                    target[key] = source[key];
                }
            }
        }
        return target;
    }
    var rformat = /([\d\D]+?)&lt;\/code&gt;/ig;
    var formats = {
        bold     : [1, 22],
        italic    : [3, 23],
        underline : [4, 24],
        inverse   : [7, 27],
        strike    : [9, 29]
    };
    var colors = {};
    [&#39;black&#39;, &#39;red&#39;, &#39;green&#39;, &#39;yellow&#39;, &#39;blue&#39;, &#39;magenta&#39;, &#39;cyan&#39;, &#39;white&#39;].forEach(function(word,i){
        colors[word] = i
    });
    colors.gray = 99;
    function format (arr, str) {
        return &#39;\x1b[&#39; + arr[0] + &#39;m&#39; + str + &#39;\x1b[&#39; + arr[1] + &#39;m&#39;;
    };
    mix(mass,{//为此版本的命名空间对象添加成员
        rword : /[^, ]+/g,
        v : version,
        &quot;@debug&quot; : true,
        root: process.cwd(),
        /**
         * 数组化
         * @param {ArrayLike} nodes 要处理的类数组对象
         * @param {Number} start 可选。要抽取的片断的起始下标。如果是负数，从后面取起
         * @param {Number} end 可选。规定从何处结束选取
         * @return {Array}
         */
        slice: function (nodes, start, end) {
            for(var i = 0,n = nodes.length, result = []; i &lt; n; i++){
                result[i] = nodes[i];
            }
            if (arguments.length &gt; 1) {
                return result.slice(start , (end || result.length));
            } else {
                return result;
            }
        },
        /**
         * 用于取得数据的类型或判定数据的类型
         * @param {Any} obj 要检测的东西
         * @param {String} str 要比较的类型
         * @return {String|Boolean}
         */
        type : function (obj, str){
            var result = class2type[ (obj == null || obj !== obj )? obj : toString.call(obj) ] || &quot;#&quot;;
            if( result.charAt(0) === &quot;#&quot;){
                if(Buffer.isBuffer(obj)){
                    result = &#39;Buffer&#39;; //返回构造器名字
                }else{
                    result = toString.call(obj).slice(8,-1);
                }
            }
            if(str){
                return str === result;
            }
            return result;
        },
        /**
         * 用于调试
         * @param {String} s 要打印的内容
         * @param {Boolean} color 进行各种颜色的高亮，使用&lt;code style=&quot;format:blod;color:red;background:green&quot;&gt;
         * format的值可以为formats中五个之一或它们的组合（以空格隔开），背景色与字体色只能为colors之一
         */
        log:function (s, color){
            if(color){
                s = s.replace(rformat,function(a,style,ret){
                    style.toLowerCase().split(&quot;;&quot;).forEach(function(arr){
                        arr = arr.split(&quot;:&quot;);
                        var type = arr[0].trim(),val = arr[1].trim();
                        switch(type){
                            case &quot;format&quot;:
                                val.replace(/\w+/g,function(word){
                                    if(formats[word]){
                                        ret = format(formats[word],ret)
                                    }
                                });
                                break;
                            case &quot;background&quot;:
                            case &quot;color&quot;:
                                var array = type == &quot;color&quot; ? [30,39] : [40,49]
                                if( colors[val]){
                                    array[0] += colors[val]
                                    ret = format(array,ret)
                                }
                        }
                    });
                    return ret;
                });
            }
            console.log(s);
        },
        /**
         * 生成键值统一的对象，用于高速化判定
         * @param {Array|String} array 如果是字符串，请用&quot;,&quot;或空格分开
         * @param {Number} val 可选，默认为1
         * @return {Object}
         */
        oneObject : function(array, val){
            if(typeof array == &quot;string&quot;){
                array = array.match(mass.rword) || [];
            }
            var result = {},value = val !== void 0 ? val :1;
            for(var i=0,n=array.length;i &lt; n;i++){
                result[array[i]] = value;
            }
            return result;
        },
        mix:mix
    });

    mass.noop = mass.error = function(){};
    &quot;Boolean,Number,String,Function,Array,Date,RegExp,Arguments&quot;.replace(mass.rword,function(name){
        class2type[ &quot;[object &quot; + name + &quot;]&quot; ] = name;
    });

    var
    rmodule = /([^(\s]+)\(?([^)]*)\)?/,
    names = [],//需要处理的模块名列表
    rets = {},//用于收集模块的返回值
    cbi = 1e4 ;//用于生成回调函数的名字
    var map = mass[&quot;@modules&quot;] = {};
    //执行并移除所有依赖都具备的模块或回调
    function resolveCallbacks(){
        loop:
        for (var i = names.length,repeat, name; name = names[--i]; ) {
            var  obj = map[name], deps = obj.deps;
            for(var key in deps){
                if(deps.hasOwnProperty(key) &amp;&amp; map[key].state != 2 ){
                    continue loop;
                }
            }
            //如果deps是空对象或者其依赖的模块的状态都是2
            if( obj.state != 2){
                names.splice(i,1);//必须先移除再执行
                var fn = obj.callback;
                rets[fn._name] = fn.apply(null,incarnate(obj.args));//只收集模块的返回值
                obj.state = 2;
                repeat = true;
            }
        }
    repeat &amp;&amp;  resolveCallbacks();
    }
    function incarnate(args){//传入一组模块名，返回对应模块的返回值
        for(var i = 0,ret = [], name; name = args[i++];){
            ret.push(rets[name]);
        }
        return ret;
    }
    function deferred(){//一个简单的异步列队
        var list = [],self = function(fn){
            fn &amp;&amp; fn.call &amp;&amp; list.push(fn);
            return self;
        }
        self.method = &quot;pop&quot;;
        self.fire = function(fn){
            while(fn = list[self.method]()){
                fn();
            }
            return list.length ? self : self.complete();
        }
        self.complete = mass.noop;
        return self;
    }

    var nativeModules = mass.oneObject(&quot;assert,child_process,cluster,crypto,dgram,dns,&quot;+
        &quot;events,fs,http,https,net,os,path,querystring,readline,repl,tls,tty,url,util,vm,zlib&quot;)
    function useNativeRequire(name,url){
        var nick = name.slice(1);
        if(nativeModules[nick]){
            map[name].state = 2;
            rets[name] = require(nick);
            resolveCallbacks();
        }else{
            url = url  || mass.root+&quot;/&quot; + nick + &quot;.js&quot;;
            try{
                require(url);
                resolveCallbacks()
            }catch(e){
                mass.stack(Function(&#39;mass.log(&quot;\033[31m&#39;+e+&#39;\033[39m&quot;)&#39;));
                mass.stack.fire();//打印错误堆栈
            }
        }
    }
    mass.mix(mass,{
        stack : deferred(),
        define:function(name,deps,callback){//模块名,依赖列表,模块本身
            if(typeof deps == &quot;function&quot;){//处理只有两个参数的情况
                callback = deps;
                deps = &quot;&quot;;
            }
            callback._name = &quot;@&quot;+name; //模块名
            this.require(deps,callback);
        },
        require:function(deps,callback,errback){//依赖列表,正向回调,负向回调
            var _deps = {}, args = [], dn = 0, cn = 0;
            (deps +&quot;&quot;).replace(mass.rword,function(url,name,match){
                dn++;
                match = url.match(rmodule);
                name = &quot;@&quot;+ match[1];//取得模块名
                if(!map[name]){ //防止重复生成节点与请求
                    map[name] = { };//state: undefined, 未加载; 1 已加载; 2 : 已执行
                    useNativeRequire(name,match[2]);//加载模块
                }else if(map[name].state === 2){
                    cn++;
                }
                if(!_deps[name] ){
                    args.push(name);
                    _deps[name] = &quot;司徒正美&quot;;//去重
                }
            });
            var cbname = callback._name;
            if(dn === cn ){//在依赖都已执行过或没有依赖的情况下
                if(cbname &amp;&amp; !(cbname in rets)){
                    map[cbname].state = 2 //如果是使用合并方式，模块会跑进此分支（只会执行一次）
                    return rets[cbname] =  callback.apply(null,incarnate(args));   
                }else if(!cbname){//普通的回调可执行无数次
                    return callback.apply(null,incarnate(args))
                }
            }
            cbname = cbname || &quot;@cb&quot;+ (cbi++).toString(32);

            if(errback){
                mass.stack(errback);//压入错误堆栈
            }
            map[cbname] = {//创建或更新模块的状态
                callback:callback,
                deps:_deps,
                args: args,
                state: 1
            };//在正常情况下模块只能通过resolveCallbacks执行
            names.unshift(cbname);
            resolveCallbacks();
        }
        
    });


    exports.mass = global.mass = mass;
})();
</pre><img src="http://www.cnblogs.com/rubylouvre/aggbug/2276390.html?type=1" width="1" height="1" alt=""><p><a href="http://www.cnblogs.com/rubylouvre/archive/2011/12/05/2276390.html">本文链接</a></p>