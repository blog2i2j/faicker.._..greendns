[![PyPI version]][PyPI]
[![Build Status](https://travis-ci.org/faicker/greendns.svg?branch=master)](https://travis-ci.org/faicker/greendns)
[![Coverage Status](https://coveralls.io/repos/github/faicker/greendns/badge.svg?branch=master)](https://coveralls.io/github/faicker/greendns?branch=master)

# greendns

A DNS recursive resolve server to avoid result being poisoned and friendly to CDN. It will qeury dns servers at the same time and don't wait for all responses. It's more efficient and quicker than [ChinaDNS](https://github.com/shadowsocks/ChinaDNS)

You must config at least two dns servers. One part is local and poisoned, the other part is unpoisoned(tunnel through VPN or use OpenDNS 443/5353 port, [dnscrypt-proxy](https://github.com/jedisct1/dnscrypt-proxy) is recommended)

## How it works

```
First filter poisoned ip with blocked iplist with -b argument.
Second,
                                       | A record is local | A record is foreign
    local and poisoned dns server      |    a              |   b
    unpoisoned dns server              |    c              |   d

From the matrix, we get the result as follows,
ac: use local dns server result
ad: use local dns server result
bc: impossible. use unpoisoned dns server result
bd: use unpoisoned dns server result

Conclusion,
Using local dns server result if returned A record is local.
Using unpoisoned dns server result if returned A record is Foreign.
```

It has two assumptions,
* the polluted domain is foreign.
* the A record in poisoned response is foreign.

## Install

```bash
pip install greendns
```

## Usage

```bash
greendns -r greendns -f etc/greendns/localroute.txt -b etc/greendns/iplist.txt
```

## Configure

```bash
greendns -r greendns -h
usage: greendns [-h] [-r HANDLER] [-p PORT] [-t TIMEOUT] [-l LOGLEVEL]
                [-m MODE] [--lds LDS] [--rds RDS] [-f LOCALROUTE]
                [-b BLACKLIST] [--rfc1918] [--cache]

optional arguments:
  -h, --help
  -r HANDLER, --handler HANDLER
                        Specify handler class, greendns|quickest (default:
                        None)
  -p PORT, --port PORT  Specify listen port or ip (default: 127.0.0.1:1053)
  -t TIMEOUT, --timeout TIMEOUT
                        Specify upstream timeout (default: 1.5)
  -l LOGLEVEL, --log-level LOGLEVEL
                        Specify log level, debug|info|warning|error (default:
                        info)
  -m MODE, --mode MODE  Specify io loop mode, select|epoll (default: select)
  --lds LDS             Specify local poisoned dns servers (default:
                        223.5.5.5:53,114.114.114.114:53)
  --rds RDS             Specify unpoisoned dns servers (default:
                        tcp:208.67.222.220:443,193.112.15.186:2323)
  -f LOCALROUTE, --localroute LOCALROUTE
                        Specify local routes file (default:
                        /home/etc/greendns/localroute.txt)
  -b BLACKLIST, --blacklist BLACKLIST
                        Specify ip blacklist file (default:
                        /home/etc/greendns/blacklist.txt)
  --rfc1918             Specify if rfc1918 ip is local (default: False)
  --cache               Specify if cache is enabled (default: False)
```

## Acknowledgements

+ @clowwindy: the author of the [ChinaDNS](https://github.com/shadowsocks/ChinaDNS)

## License

This project is under the MIT license. See the [LICENSE](LICENSE) file for the full license text.
