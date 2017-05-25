---
layout: post
title: "拖拽文件异步上传"
date: 2015-08-11 09:40:58 +0800
categories: [html5, ajax, 文件上传]
tags: [html5, ajax, 文件上传]
comments: true
---

## 文件目录为

> [文件目录图](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMcAAABWCAIAAAD0XRV3AAAFLklEQVR4nO2dPWvbQBjH/bW0BOPvUOhc6HVvpvZDaOzQdO6S2Elx6oBJMyTZDAkhQ6gb6slkDS4ePAR1kC3fy3N683PSSfn/EEGW7kWQH3eH9DxSJwKAm07dFwBaCKwC/MAqwA+sAvwwW/Xryztt+zv5wdsF8B/nVjkQayQ6YrR7G0E4ZbkcYFCFVeS2QyewyncIq05PT/sUw+EwszlYBSLSqvl8Tlo1n88zm8tjDKxqPfQMOB6PNaXG43Ge5kpaNQ2DRJTtfrw3Ep01mxKyVduzHVm1aRgQR+XCHVjlDtqqxWIxGAwSpQaDwWKxyNMcu1WKS+v9xKqRkN3YFpiGYnNUqaXKCKucYV2tX19fJ1ZdXV3lbM7BWGUW2lilz2JKeX200gpjBnSJ1arVanV8fBwPVKvVKmdzdVsVhNO1UOszciVYVRVpdxZub2/7/f7d3V3+5nawStJAngFl2TJnwPiHZIxaS20MVjkj437V+fl5oeZK31lIJqxACGWsEsZiPXu1vp39pNbkskEYYqxyiM93QbUZEDQGn58uw6qmAqsAPz5bBZoKrAL8wCrAD6wC/MAqwA+sAvzAKsCPR1Yhk6I1eG1VbrHWD/7eHyRPqTniRUFZ+K06Ozt7fn4uUXGnJ9Pb59Gwqn74rYoD/S4vL/NHZcWUtoqKlYJVdeLKqjjc7/7+Pn/F0pkUsMo3HFqV5HvlSc6JylolBViJn9u5kI7BQkhVNTi3Kubh4SGzIsNYRVglD2QIgqgI51adnJw8Pj7mqejEKjVZC8NVNbi1ajKZvLy85Kzoyip4VDmurLq4uFgul4UqOpsB5RUWJsAq4LdqOBw+PT2VqMhwZ4FcrdN5zMAhvt9bL5tJAerEI6tAa4BVgB9YBfiBVYAfWAX4gVWAH1gF+PHLqm8Wbm5u6r40UIBmWHV4eAixGoSPVpkHl8vl0dERxGoKnlr18c+HeIvUAYyoI72nL5vyIQy7Bz+8ovBUf616G76JrdJO6cAq//DXqr1uzxyxdu2A2apCwaWwqiZgVTvw2qq9bi+ZB1NnQClEPRRGJLHtkxB6loT8zmQl2M9ovCNGSrNB+DsuMFrHcsnnNx3Cqpowx6r4b5TXqg6Vbkp+EoLOklgfJQYmsnF5rBqJxKDYJ/2l8bCqJna2ShYlCKf2l/dbsyTiE+ZkRzWuW5UUIPdhVU1UahW9wIJVDPhrlbxFpa2yfhKCzpKIm1CnOFNDWJWBp1YVOJVhlf2TEEaWhLTq2izcM6zatJGs1mFVFEV+WpVC3RcIcgGrAD9+WQXaAawC/MAqwA+sAvzAKsAPrAL8+GWV7YYCYoubRTOsQjZEs/DRKvMgsiGaRTOs4ru3zvEwrkBA6St9gaRfVs1ms9lsVvRUEWBVFfhl1c/v/+Rtr9vTjsTbDj3AqiqAVaXagFWpeGdVnATxef8gZV+pI0fOqd9JEqNtaJUUW0d8M0LJfKDfTmtLqZA7JrvTcihSCrcHH63K/KvUsVul/HOpN2ar6TabikKKt6Nfr01bZemOzqGgCrcHH63a6/Y+7X81fUqOK3XSxiqzkO3t/kp5fbSyBb9bqqvdpcclmz/bgI9WkWsp+bhSh82qIJyqoe2kG0Td9O5gVd2UtUqLNld3lR/2GdAIT1drUSkV2qRGd0dbRRVuD35ZVe5+VTJhBUIoY5UwF8SZq/Xt7Ce1Zkmp0NJN6e4sYxVRuD34ZRVf3HrF00qh7lo45WnAKhZglYJfVvEBq+qkrVaBOoFVgB9YBfiBVYCf/01ox8u3OIbxAAAAAElFTkSuQmCC)

## 没做ie浏览器兼容,用于测试，index.html代码如下

```
    var dragUpload = {
        file: document.getElementById('file'),
        fileArea: document.getElementById('fileArea'),
        init:function() {
            var self = this;
            this.fileArea.onclick = function() {
                var event = document.createEvent('MouseEvents');//调用document对象的 createEvent 方法得到一个event的对象实例。
                 /*initEvent接受3个参数：
                 事件类型，是否冒泡，是否阻止浏览器的默认行为*/
                event.initEvent('click', true, false);
                self.file.dispatchEvent(event);//触发控件的单击事件
            };
            this.file.onchange = function() {
                self.upload(this.files);
            };
            this.fileArea.ondragenter = function(ev) {
                self.dragEnter(ev,this);
            };
            this.fileArea.ondragover = function(ev) {
                self.dragOver(ev,this);
            };
            this.fileArea.ondrop = function(ev) {
                self.drop(ev,this);
            };
        },
        upload:function(fs) {//上传文件
            for(var i = 0,len = fs.length; i < len; i++) {
                this.sendFile(fs[i]);
            }
        },
        dragEnter:function(ev,my) {//文件进入回调函数
            ev.preventDefault();
            console.log('dragEnter');
            my.className = 'up-area hover';
        },
        dragOver:function(ev,my) { //文件经过回调函数
            ev.preventDefault();
            console.log('dragOver');
        },
        drop:function(ev,my) {//放入文件回调函数
            ev.preventDefault();
            console.log('drop');
            var dt = ev.dataTransfer;//获取文件对象
            my.className = 'up-area';
            dragUpload.upload(dt.files);
        },
        sendFile:function(file) {//上传文件主函数
            var xhr = new XMLHttpRequest();
            var fd = new FormData();
            fd.append('file', file);
            xhr.onreadystatechange = function() {
                if(xhr.readyState ===4 && xhr.status === 200) {
                    document.getElementById('console').innerHTML += '<br/>'+xhr.responseText;
                }
            };
            xhr.open('POST', 'upload.php ',true);
            xhr.send(fd);
        }
    };
    dragUpload.init();
```
## upload.php代码为

```
<?php
$file = $_FILES['file'];//获取文件内容
//存入上传的文件
$path = 'file';
if($file['size'] > 10*1024*1024){
    echo '{"error":"1000","message":"上传的文件超过限制，最大10M"}';
}else{
    $path .= '/file_'.time().'.png';
    move_uploaded_file($file['tmp_name'],$path);
    echo '上传成功：'. json_encode($file);
}
```
