## js代码解析步骤

- **函数词法分析阶段**

  1. 第一步：**预解析，即把函数声明、变量声明提到当前作用域最顶端**

  2. 第二步：**函数运行前，生成一个AO(active object)对象，该对象就是一个容器，用来放所有的变量以及对应的值，具体分析如下:**

     ~~~javascript
     （1）参数分析：形参相当于声明在函数内部的局部变量，把形参放到AO对象里面，形成AO的属性，传递过来	  的实参值即该属性的值，刚开始未传时为undefined。
     （2）变量声明分析：对于带有var的变量，如果AO上没有此属性，则添加此属性，值是undefined，如果AO上	已经有此属性，则没影响（不覆盖）。
     （3）函数声明分析：函数名为属性名，整个函数为值，如果此前该属性已经存在，则覆盖。
     ~~~

  3. 第三步：*变量访问规则：*`访问变量，优先访问的是在自己作用域链上声明的变量，如果自己作用域链上没有声明这个变量，那么就往上一级去找有没有声明这个变量，如果有就访问，如果没有就继续往上找有没有声明，直到找到0级作用域链上，如果有，就访问，没有就报错,不规则变量在函数内也是全局变量`

- **代码执行阶段**

  `当有异步时 eg：定时器函数，ajax   会放到事件队列里等所有代码执行完才会执行,正常的则从上往下执行代码  `

- **实例**

  ~~~javascript
  <script>
    	1.  var a = 10;                              
                                                      
  	  function aaa(a) {									 
            alert(a);									   
  													  
            var a = 20;
        }

        aaa(a);

    变形： var a
    		function aaa(a) {
            	var a
              alert(a);

               a= 20;
          }
      	a= 10;

          aaa(a);
     解：第一步：预解析处理 见变形
     	   第二步：var AO = {}
  			参数：AO.a =undefined a = 10;
  			变量声明：已经有a属性，无需生成 
              函数声明：AO.aaa=function aaa{ alert(a); var a = 20;};
  ---------------------------------------------------------------------------
  	2.function aaa() {

              bbb();

              var bbb = function () {
                  console.log("hello");
              };

          }
          aaa();
  	变形：function aaa() {
  			var bbb
              bbb();

              bbb = function () {
                  console.log("hello");
              };

          }
          aaa();
      解：第一步：预解析处理 见变形
     	    第二步：var AO = {}
  			参数：无
  			变量声明：AO.bbb =undefinde;  所以bbb（）当做函数调用报错
              函数声明：无，这里函数当做值来被赋值，不存在预解析
  ---------------------------------------------------------------------------
  	3.function a(b) {
              
           console.log(b);

           function b() { 
               console.log(b);
            }
            b();
        }
  	  a(1);
      变形：function a(b) {
              function b() { 
                  console.log(b);
              }
              console.log(b);
        
              b();
          }
          a(1);
  	解：第一步：预解析处理 见变形
     	    第二步：var AO = {}
  			参数：AO.b = undefined b=1;
  			变量声明：无
              函数声明：AO.b=function b{ console.log(b);};存在AO.b 覆盖上面的AO.b
  ---------------------------------------------------------------------------
  	4.function a(b) {
            alert(b);
            b = function () {
                alert(b);
            }
            b();
          }
        a(1);
  	
  	解：第一步：无预解析
     	    第二步：var AO = {}
  			参数：AO.b = undefined b=1;
  			变量声明：无 里面b为不规则变量
              函数声明：无
          第三步：从上往下，正常执行代码 第一个alert（b），b此时为1，接着b被赋值函数调用，此时b为函数
  ---------------------------------------------------------------------------
  	5.function t(age){
  		 var g='hello';
  		 alert(g);
  			
  		 function g(){
  				
  		 }
        	 g();
  		 alert(g);
  	 }
       t(null);
  	变形：function t(age){
        		var g	
              function g(){
  				
  			}
  			g='hello';
  			alert(g);
        		g();
  			alert(g);
  		}
  		t(null);
  	解：第一步：预解析处理 见变形
     	    第二步：var AO = {}
  			参数：AO.age = undefined age=null; 无用，干扰视听
  			变量声明：AO.g = undefinde;
              函数声明：AO.g=function g{};存在AO.g 覆盖上面的AO.g
          第三步：从上往下，正常执行代码 第一个alert（g），g此时为hello，接着g()调用，此时g为变量，所以报错g is not a function
  <script>
  ~~~

- 结束语：**当然，一般情况下，像上面的变量名跟函数名的命名冲突都是会避免的，以上方法适用面试中99.9%的js作用域题，剩下的0.1%留给炒鸡变态题**

  ​

  ​



​		

​	





