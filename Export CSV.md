
Export CSV

```javascript
exportCSV(data,fileName,type){
var file = new Blob(["\ufeff + data"],{type:type});
if(window.navigator.msSaveOrOpenBlob)
  //IE10
  window.navigator.msSaveOrOpenBlob(file,fileName);
else{
    //other
    var a = document.createElement("a"),
    url = URL.createObjectURL(file);
    a.href = url;
    a.download = fileName;
    document.body.appendChild(a);
    a.click();
    setTimeout(function(){
    document.body.removeChild(a);
    window.URL.revokeObjectURL(url);
    },0);
  }
}




```
