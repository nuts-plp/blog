# openssl的安装及使用

[openssl的第三方下载地址](http://slproweb.com/products/Win32OpenSSL.html)

## 证书自签和颁发的流程

生成证书配置文件openssl.cnf（根据配置文件创建出对应的文件和文件夹）

~~~bash
[ CA_default ]

dir		= ./demoCA		# Where everything is kept
certs		= $dir/certs		# Where the issued certs are kept 证书保存目录
crl_dir		= $dir/crl		# Where the issued crl are kept 证书吊销列表
database	= $dir/index.txt	# database index file. 给哪些人颁发过证书
#unique_subject	= no			# Set to 'no' to allow creation of
					# several certs with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs. 新生成证书存放目录

certificate	= $dir/cacert.pem 	# The CA certificate 证书颁发机构自己的证书
serial		= $dir/serial 		# The current serial number 证书序列号 注意这是一个文件，创建后写入一个起始序列号
crlnumber	= $dir/crlnumber	# the current crl number   证书吊销列表工作号
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL 当前吊销的证书文件
private_key	= $dir/private/cakey.pem# The private key 证书颁发机构自己的私钥

x509_extensions	= usr_cert		# The extensions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options

# Extension copying option: use with caution.
# copy_extensions = copy

# Extensions to add to a CRL. Note: Netscape communicator chokes on V2 CRLs
# so this is commented out by default to leave a V1 CRL.
# crlnumber must also be commented out to leave a V1 CRL.
# crl_extensions	= crl_ext

default_days	= 365			# how long to certify for 证书有效期
default_crl_days= 30			# how long before next CRL 吊销证书存放时间
default_md	= default		# use public key default MD
preserve	= no			# keep passed DN ordering
~~~

1、在PEM路径下创建一个demoCA文件夹，并根据以上配置文件创建好对应的文件和文件夹，注意serial是一个文件并为其写入初始序列号

2、创建一个私钥

~~~bash
openssl genrsa -out private/cakey.pem 2048 
~~~

3、因为自己是颁发机构（CA),根据上一步的私钥为自己颁发自签证书在demoCA路径下

~~~bash
openssl req -new -x509 -key private/cakey.pem -out cacert.pem
~~~

4、下面准备为别人签发证书，首先在PEM目录下创建http文件夹，并在http文件夹下创建ssl文件夹，并在该路径下创建私钥

~~~bash
openssl genrsa -out http.key 1024
~~~

5、根据生成的私钥申请证书并填写相关信息

~~~bash
openssl req -new -key http.key -out http.csr
~~~

6、签发证书

~~~bash
openssl ca -in D:\OpenSSL-Win64\bin\PEM\demoCA\http\ssl\http.csr -out D:\OpenSSL-Win64\bin\PEM\demoCA\http\ssl\http.crt -days 365
~~~

注意：颁发证书的机构与证书使用人的国家省份公司名称需一致