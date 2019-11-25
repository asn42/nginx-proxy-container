# ASN nginx proxy container

## Requirements

- docker
- docker-compose

## Install

``` sh
docker-compose up -d
```

### Add a (proxied) webapp

Add the `A` and `AAAA` entries in the authoritative DNS server, then run the
container with a `docker-compose.yml` file that looks like this:

``` yml
version: '3.6'

services:
  the-webapp:
    image: someuser/some-webapp
    expose:
      - "3000"
    environment:
      - VIRTUAL_PORT=3000
      - VIRTUAL_HOST=webapp.sansnom.org
      - LETSENCRYPT_HOST=webapp.sansnom.org
      - LETSENCRYPT_EMAIL=sysadmin@sansnom.org
    networks:
      - web_proxy

networks:
    web_proxy:
      external:
        name: web_proxy
```

- `3000` is the port used by the app's web server in this example
- `webapp.sansnom.org` is the domain name to reach the web app
- `sysadmin@sansnom.org` is the e-mail address for certificate renewal reminders


## What's inside?

### docker-compose.yml

Defines 3 containers:

- nginx-proxy, regular nginx server
- nginx-docker-gen, configuration generator for nginx using docker-gen
- letsencrypt-http, letsencrypt client (simp_le) to get domain name certificates

### nginx.tmpl

Contains the default template from jwilder/nginx-proxy (downloaded 2019-10-24)
used to generate nginx configuration file.

## Goal and current state

We want

- safety (for access and data)
- stability (against crashes)
- free software (if we run it, you too should be able to)

We rely on images made by other people as a starting point, but the plan is to
build the images ourselves in the future.

We are using the HTTP-01 challenge, but we plan to use the DNS-01 challenge to
get a wildcard certificate in the future.

## License

### Original code/config/content

> © 2019 Association Sans Nom and contributors (see CONTRIBUTORS.md)
> 
> This program is free software: you can redistribute it and/or modify it
> under the terms of the GNU Affero General Public License as published by the
> Free Software Foundation, either version 3 of the License, or (at your
> option) any later version.
> 
> This program is distributed in the hope that it will be useful, but WITHOUT
> ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or
> FITNESS FOR A PARTICULAR PURPOSE. See the GNU Affero General Public License
> for more details.
> 
> You should have received a copy of the GNU Affero General Public License
> along with this program. If not, see <https://www.gnu.org/licenses/>.

### nginx.tmpl

See https://github.com/jwilder/nginx-proxy/blob/master/nginx.tmpl

> The MIT License (MIT)
> 
> Copyright (c) 2014 Jason Wilder
> 
> Permission is hereby granted, free of charge, to any person obtaining a copy
> of this software and associated documentation files (the "Software"), to deal
> in the Software without restriction, including without limitation the rights
> to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
> copies of the Software, and to permit persons to whom the Software is
> furnished to do so, subject to the following conditions:
> 
> The above copyright notice and this permission notice shall be included in all
> copies or substantial portions of the Software.
> 
> THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
> IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
> FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
> AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
> LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
> OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
> SOFTWARE.
