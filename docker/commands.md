docker pull registry.storm.io/xianzhi/cloud-waf:20200928.0
docker save -0 pingan_update_20200928.tar.gz registry.storm.io/xianzhi/cloud-waf:20200928.0
docker save -o  pingan_update_20200928.tar.gz registry.storm.io/xianzhi/cloud-waf:20200928.0
docker load -i pingan_update_20200928.tar.gz
sz pingan_update_20200928.tar.gz
