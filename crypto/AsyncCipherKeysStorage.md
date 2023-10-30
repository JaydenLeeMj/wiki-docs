# 非对称加密密钥对的存储方案

Intro: key 本质上就是一个数字, 但是是二进制的, 可以说是一个 bit stream. 使用不用的编码方式可以将其转为可以打印显示的字符(more human friendly), 而Base64就是这其中最简单方案.


## I.Sample: 如何生成密钥对

直接生成样例(使用OpenSSL): 
```bash
# 1.Gen private key
# 1024 is the key size (size < 2048)
openssl genrsa -out rsa_private_key.pem 1024

# 2.Gen public key based on the private key
openssl rsa -in rsa_private_key.pem -out rsa_public_key.pem -pubout

# PEM文件只用一种文件的额存储协议,没有特殊的加密,只是Base64对密钥对进行简单编码
```


私钥文件样例:
```pem
-----BEGIN RSA PRIVATE KEY-----
MIICWwIBAAKBgQChDzcjw/rWgFwnxunbKp7/4e8w/UmXx2jk6qEEn69t6N2R1i/L
mcyDT1xr/T2AHGOiXNQ5V8W4iCaaeNawi7aJaRhtVx1uOH/2U378fscEESEG8XDq
ll0GCfB1/TjKI2aitVSzXOtRs8kYgGU78f7VmDNgXIlk3gdhnzh+uoEQywIDAQAB
AoGAaeKk76CSsp7k90mwyWP18GhLZru+vEhfT9BpV67cGLg1owFbntFYQSPVsTFm
U2lWn5HD/IcV+EGaj4fOLXdM43Kt4wyznoABSZCKKxs6uRciu8nQaFNUy4xVeOfX
PHU2TE7vi4LDkw9df1fya+DScSLnaDAUN3OHB5jqGL+Ls5ECQQDUfuxXN3uqGYKk
znrKj0j6pY27HRfROMeHgxbjnnApCQ71SzjqAM77R3wIlKfh935OIV0aQC4jQRB4
iHYSLl9lAkEAwgh4jxxXeIAufMsgjOi3qpJqGvumKX0W96McpCwV3Fsew7W1/msi
suTkJp5BBvjFvFwfMAHYlJdP7W+nEBWkbwJAYbz/eB5NAzA4pxVR5VmCd8cuKaJ4
EgPLwsjI/mkhrb484xZ2VyuICIwYwNmfXpA3yDgQWsKqdgy3Rrl9lV8/AQJAcjLi
IfigUr++nJxA8C4Xy0CZSoBJ76k710wdE1MPGr5WgQF1t+P+bCPjVAdYZm4Mkyv0
/yBXBD16QVixjvnt6QJABli6Zx9GYRWnu6AKpDAHd8QjWOnnNfNLQHue4WepEvkm
CysG+IBs2GgsXNtrzLWJLFx7VHmpqNTTC8yNmX1KFw==
-----END RSA PRIVATE KEY-----
```

密钥文件最终将数据通过Base64编码进行存储。可以看到上述密钥文件内容每一行的长度都很规律。这是由于**RFC2045**中规定：*The encoded output stream must be represented in lines of no more than 76 characters each*。也就是说`Base64`编码的数据每行最多不超过76字符，对于超长数据需要按行分割.

将没有加密的私钥可以进行按照 `PKCS#12` 的规范存储: 
```bash
# -nocrypt 不采用任何二次加密
openssl pkcs8 -topk8 -in rsa_private_key.pem -out pkcs8_rsa_private_key.pem -nocrypt
```

转格式后(pkcs8_rsa_private_key.pem): 
```pem
-----BEGIN PRIVATE KEY-----
MIICdQIBADANBgkqhkiG9w0BAQEFAASCAl8wggJbAgEAAoGBAKEPNyPD+taAXCfG
6dsqnv/h7zD9SZfHaOTqoQSfr23o3ZHWL8uZzINPXGv9PYAcY6Jc1DlXxbiIJpp4
1rCLtolpGG1XHW44f/ZTfvx+xwQRIQbxcOqWXQYJ8HX9OMojZqK1VLNc61GzyRiA
ZTvx/tWYM2BciWTeB2GfOH66gRDLAgMBAAECgYBp4qTvoJKynuT3SbDJY/XwaEtm
u768SF9P0GlXrtwYuDWjAVue0VhBI9WxMWZTaVafkcP8hxX4QZqPh84td0zjcq3j
DLOegAFJkIorGzq5FyK7ydBoU1TLjFV459c8dTZMTu+LgsOTD11/V/Jr4NJxIudo
MBQ3c4cHmOoYv4uzkQJBANR+7Fc3e6oZgqTOesqPSPqljbsdF9E4x4eDFuOecCkJ
DvVLOOoAzvtHfAiUp+H3fk4hXRpALiNBEHiIdhIuX2UCQQDCCHiPHFd4gC58yyCM
6Leqkmoa+6YpfRb3oxykLBXcWx7DtbX+ayKy5OQmnkEG+MW8XB8wAdiUl0/tb6cQ
FaRvAkBhvP94Hk0DMDinFVHlWYJ3xy4pongSA8vCyMj+aSGtvjzjFnZXK4gIjBjA
2Z9ekDfIOBBawqp2DLdGuX2VXz8BAkByMuIh+KBSv76cnEDwLhfLQJlKgEnvqTvX
TB0TUw8avlaBAXW34/5sI+NUB1hmbgyTK/T/IFcEPXpBWLGO+e3pAkAGWLpnH0Zh
Fae7oAqkMAd3xCNY6ec180tAe57hZ6kS+SYLKwb4gGzYaCxc22vMtYksXHtUeamo
1NMLzI2ZfUoX
-----END PRIVATE KEY-----
```


## PEM文件类型

除了pem文件，还有其他常见的公钥证书文件格式，如`DER`、`PKCS7`和`PKCS12`等。其中，`DER`是一种二进制格式，`PKCS7`和`PKCS12`则是基于`DER`格式的扩展格式。

A: pem文件是一种常见的公钥证书文件格式，用于存储和传输加密密钥和数字证书。pem文件通常包含Base64编码的密钥或证书信息，并以“—–BEGIN”和“—–END”开头和结尾。

除了pem文件，还有其他常见的公钥证书文件格式，如`DER`、`PKCS7`和`PKCS12`等。其中，DER是一种二进制格式，`PKCS7`和`PKCS12`则是基于`DER`格式的扩展格式。
pem文件通常用于在不同系统之间共享公钥证书，如在Web服务器和浏览器之间共享SSL证书。同时，pem文件也可以用于存储和传输加密密钥，如在SSH连接中使用的私钥文件。

需要注意的是，pem文件虽然常见，**但并不是所有的加密系统都支持该格式**。因此，在使用pem文件时，需要根据具体情况选择合适的加密算法和文件格式。

