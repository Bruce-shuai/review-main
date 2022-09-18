HTTP3
相关文章：
https://www.stackscale.com/blog/http3/
https://thenewstack.io/http-3-is-now-a-standard-why-use-it-and-how-to-get-started/

> 在2022年6月份 HTTP3已经成为IETF标准

### HTTP3有什么变化(相比于HTTP2或之前的HTTP版本)
#### HTTP2的缺陷
* `HTTP2`是基于`TCP`协议实现的，而`TCP`的一个特性就是连接可靠，如果在传输数据的过程中，有一个数据包被丢失,那么整个`TCP`的连接就会处于一个停止等待状态，需要等待丢失的数据包重新发过来才能重新传输。(简称：TCP的队头阻塞)

* TCP的建立连接是需要握手过程的，同样会影响传输速度。

#### QUIC协议 + UDP
上面提到了TCP的局限性。而由谷歌提出来的QUIC协议，就实现了 数据传输可靠、加密以及其他的TCP+TLS中的优势，并结合UDP解决TCP的缺陷。从而实现的HTTP3。 (简单而言即取TCP之长补UDP之短)

<img src="https://raw.githubusercontent.com/w-l-l/BrowserPrinciple/main/%E6%B5%8F%E8%A7%88%E5%99%A8%E4%B8%AD%E7%9A%84%E7%BD%91%E7%BB%9C/HTTP3%EF%BC%9A%E7%94%A9%E6%8E%89TCP%E3%80%81TCL%E5%8C%85%E8%A2%B1%EF%BC%8C%E6%9E%84%E5%BB%BA%E9%AB%98%E6%95%88%E7%BD%91%E7%BB%9C/img/http2-http3-agreement-stack.png" alt="http2-pk-http3" width="300px"/>

##### 细说 QUIC协议

QUIC协议：
* 实现了类似TCP的流量控制、数据可靠性的功能。具备数据包重传、拥塞控制以及其他TCP中存在的特性
* 集成了TLS的加密功能，并且减少了握手所花费的RTT的个数
* 实现了HTTP/2的多路复用，并且实现了数据流的单独船速，解决了TCP中队头阻塞问题
* 快速的握手功能，因为QUIC是基于UDP的，所以只需要0-1次RTT就可建立连接。