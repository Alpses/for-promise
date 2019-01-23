# for-promise
关于for循环里面异步操作的问题

# Welcome !

Hi! I'm what i am . I will explore the **The Explorer** below.


# Issue

Firstly, a simple issue : print out a value of 0-4 every 1 second. it is a wrong writing.

    function test() {
        for (var i = 0; i < 5; ++i) {
            setTimeout(function() {
                console.log("value is :", i);
            }, 1000);
        }
    }
    test();

The result：

    value is : 5
    value is : 5
    value is : 5
    value is : 5
    value is : 5

 **What is wrong! Amazing**

## Single thread && Asynchronous problem

The root cause is single thread && Asynchronous problem in JS.
The solution:

    function test1(par) {
        if (par < 5) {
            console.log("value is :", par);
            setTimeout(function() {
                test1(par + 1);
            }, 1000)
        }
    }
    test1(0);

## async && await

If you use **async、await** :

    var asyncFunc = function(i) {
        return new Promise(function(resolve, reject) {
            setTimeout(function() {
                console.log("value is : ", i);
                resolve();
            }, 1000);
        });
    }
    var test2 = async function() {
        for (var i = 0; i < 5; i++) {
            await asyncFunc(i);
        }
    }
    test2();

 
 
  **What a suprise! Amazed**
  
## The same issue in NodeJS


You may encounter the same issue in NodeJS:  for example  :  **Find subfolders in a folder**.
That is:

    var filesArr=[];
    fs.readdir('folder',function(err,files){
    	if(err){
    	    console.log(error);
    	}else{
    	 (function getFile(i){
    	     if(i==files.length){
    	         console.log(filesArr);
    	         return false;
    	     }
    	     fs.stat('./folder/'+files[i],function(error,stats){ 
    	         if(stats.isDirectory()){
    	             filesArr.push (files[i]);
    	         }
    	         getFile(i+1);
    	     })
    	 })(0)
    	}
    })

## However , it can be simple in ES7.


    fs.readdir('./folder',function(err,files){
        if(err) return ;
        findF(files);
    })
    var findF = async function(arr){
        var fileArr = [];
        for(var i=0;i<arr.length;i++){
            let arrs  = await fileStat(fileArr,arr[i]);
            if(i==arr.length-1) console.log("Folder Arr：",arrs)
        }
    }
    function fileStat(fileArr,file){
        return new Promise((resolve,reject)=>{
            fs.stat('./folder/'+file,function(error,stats){
                if(error) return false;
                stats.isDirectory() ? fileArr.push(file):{};
                resolve(fileArr);
            })
        })
    }


