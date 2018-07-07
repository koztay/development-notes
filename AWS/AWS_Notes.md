
1. [http://markdowntables.mrvautin.com/](http://markdowntables.mrvautin.com/)

# 1. AWS Regions:

|Region Name|Region|Endpoint|Protocol|
|--- |--- |--- |--- |
|US East (Ohio)|us-east-2|rds.us-east-2.amazonaws.com|HTTPS|
|US East (N. Virginia)|us-east-1|rds.us-east-1.amazonaws.com|HTTPS|
|US West (N. California)|us-west-1|rds.us-west-1.amazonaws.com|HTTPS|
|US West (Oregon)|us-west-2|rds.us-west-2.amazonaws.com|HTTPS|
|Asia Pacific (Tokyo)|ap-northeast-1|rds.ap-northeast-1.amazonaws.com|HTTPS|
|Asia Pacific (Seoul)|ap-northeast-2|rds.ap-northeast-2.amazonaws.com|HTTPS|
|Asia Pacific (Osaka-Local)|ap-northeast-3|rds.ap-northeast-3.amazonaws.com|HTTPS|
|Asia Pacific (Mumbai)|ap-south-1|rds.ap-south-1.amazonaws.com|HTTPS|
|Asia Pacific (Singapore)|ap-southeast-1|rds.ap-southeast-1.amazonaws.com|HTTPS|
|Asia Pacific (Sydney)|ap-southeast-2|rds.ap-southeast-2.amazonaws.com|HTTPS|
|Canada (Central)|ca-central-1|rds.ca-central-1.amazonaws.com|HTTPS|
|China (Beijing)|cn-north-1|rds.cn-north-1.amazonaws.com.cn|HTTPS|
|China (Ningxia)|cn-northwest-1|rds.cn-northwest-1.amazonaws.com.cn|HTTPS|
|EU (Frankfurt)|eu-central-1|rds.eu-central-1.amazonaws.com|HTTPS|
|EU (Ireland)|eu-west-1|rds.eu-west-1.amazonaws.com|HTTPS|
|EU (London)|eu-west-2|rds.eu-west-2.amazonaws.com|HTTPS|
|EU (Paris)|eu-west-3|rds.eu-west-3.amazonaws.com|HTTPS|
|South America (Sao Paulo)|sa-east-1|rds.sa-east-1.amazonaws.com|HTTPS|


# 2. Copy bucket from one region to another:
* Önce target region için bucket yarat 
* Permission 'ları public yap (her iki bucket için)
* Şu komut ile kopyalamayı başlat:
`$aws s3 sync s3://<source-bucket>/ s3://<target-bucket>/ --region <target-region> --acl public-read`
Eğer `--acl public-read` opsiyonunu koymazsan patlıyor, permission hatası veriyor.
* Örnek :
`$aws s3 sync s3://my_source_bucket/ s3://my_destination_bucket/ --region eu-central-1 --acl public-read`
* Sonra permissionları public eski haline getir.

# 3. django Cloudfront distribution kullanımı:
* Create a cloudfront distribution from existing S3 bucket with default settings
* Aşağıdaki satıra benzer bir satırı distribution'dan al ve production.py 'a ekle:
`AWS_S3_CUSTOM_DOMAIN = "d1a9w4ni6rsc6h.cloudfront.net"`

