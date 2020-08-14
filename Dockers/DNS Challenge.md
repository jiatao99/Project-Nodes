# Let's Encrypt and DNS Challenge

Let's Encrypt is a great tool to provide free SSL certificate (only valid for 90 days). There is a tool called certbot which can automatically create/renew a SSL certificate.

To validate you own the domain which the SSL tight with, there are 3 ways:

1. HTTP-01 challenge: let's encrypt make HTTP call to match the _acme-challenge token.
  - Can NOT create wild card certificate
  - Must have public port 80 open
2. DNS-01 challenge: let's encrypt call DNS server to retrieve TXT record contains the  _acme-challenge token.
3. TTL-01 challenge: not quite sure how it works.

Must docker image support http-01 challenge out of box. But if you like me who's ISP block the port 80, you might not be able to use this method.

You can manually install/renew the SSL like below:

```
 certbot certonly  \
     --dns-cloudflare \
     --dns-cloudflare-credentials /etc/letsencrypt/cloudflare.ini \
     --server https://acme-v02.api.letsencrypt.org/directory \
     -d '*.example.com'

# renew the SSL
certbot renew --dry-run 
```

Here is the content in /etc/letsencrypt/cloudflare.ini

```
# Cloudflare API token used by Certbot
# dns_cloudflare_api_token = API token.

# Cloudflare API credentials used by Certbot
dns_cloudflare_email = your_email@gmail.com
dns_cloudflare_api_key = YOUR_API_KEY_IN_CLOUDFLARE
```

> CloudFlare does NOT support free domain like .ml; .tk; .ga etc
>
> You can register those free domain name at www.freenom.com
> Cheap place to register your domain: https://porkbun.com
>
> After registration, you can transfer your domain name lookup provider to www.cloudflare.com for free
