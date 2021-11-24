# [네트워크] HTTP 1.1 VS HTTP 2.0

들어가기 전에
웹 개발자에게 있어서 HTTP는 빼놓을 수 없죠.

HTTP는 1996년 1.0 버전으로 처음 release 되고 1999년 1.1 버전이 등장하였습니다.

그리고 1.1 버전은 HTTP 2.0이 등장하기까지 무려 15년 동안 지속되었습니다.

 

하지만 시간이 지남에 따라 웹에서 담아야 할 정보는 점점 늘어났고, 지금은 하나의 웹사이트에 수 많은 멀티미디어 리소스들과 비동기 요청들이 발생합니다.

이런 상황에서 더 이상 HTTP 1.1은 버티기 힘들었고 HTTP 2.0이 등장하게 되었습니다.

왜 HTTP 1.1이 버티기 힘들었으며, HTTP 2.0은 어떤 점이 좋은지 알아봅시다.

 

## HTTP 1.1이 어떻길래
HTTP Pipelining

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/HTTP/%231.png"/>

HTTP 1.0은 기본적으로 Connection 당 하나의 요청을 처리할 수 있습니다.

그렇기 때문에 동시전송은 불가능하고 하나의 요청에 대한 응답이 온 후 다음 요청을 처리하게 됩니다.

위에서도 말했듯이 수 많은 멀티미디어 리소스들이 있는 상황에서 이러한 특징은 Network Latency를 발생시킵니다.

 

이를 위해 HTTP 1.1에서 HTTP Pipelining 이 도입되었습니다.

이는 TCP 안에 두 개 이상의 HTTP 요청을 담아 Network Latency을 줄이는 방식입니다.

하지만 이는 정확히 구현하기 힘들 뿐 아니라 HOL Blocking이 발생합니다.

 

## HOL(Head-of-Line) Blocking
HOL은 Head of Line의 줄임말로서 앞선 요청에 의해 뒤에 요청이 지연되는 것을 의미합니다.

HTTP Pipelining 을 통해 한 번에 여러 개의 이미지를 요청하는 경우를 생각해봅시다.

가장 앞에 요청한 이미지가 응답이 지연되면 두, 세번째 이미지도 지연이 발생합니다.

TCP 안에 여러 개의 HTTP 요청이 왔으므로 완료된 응답부터 보내면 되지 않을까라고 생각할 수 있지만 서버는 TCP에서 요청을 받은 순서대로 응답을 해야합니다.

 

## 무거운 Header
클라이언트와 서버 간에 수 많은 http 요청이 발생할 것이고 header의 정보는 대부분 동일합니다.

하지만 HTTP 1.1에서는 이러한 헤더를 중복해서 계속 보낼 뿐 아니라 cookie 정보 역시 매 요청마다 헤더에 포함되어 전송됩니다.

즉 불필요한 데이터를 주고 받는데 네트워크 자원이 소비되는 문제가 발생합니다.

 

HTTP 2.0이 등장하기 이전에
HTTP 2.0이 등장하기 전, 개발자들이 마냥 손 놓고 있지만은 않았습니다.

HTTP 1.1 안에서 위에서 봤던 문제점을 극복하기 위해 노력했습니다.

 

## Image Spriting


여러 이미지 파일들에 대해 각각 요청을 하기 보다 한 번에 요청으로 끝내기를 택했습니다.

여러 이미지를 모아 하나의 큰 이미지를 만든 후, CSS로 해당 이미지의 좌표값을 지정해서 사용합니다.

 

## Domain Sharding


하나의 Domain에 대해 여러 개의 Connection을 생성하여 병렬로 요청을 보냅니다.

하지만 브라우저 별로 Domain당 Connection 개수의 제한이 존재하므로 근본적인 해결책이 될 수 없습니다.

 

## CSS, Javascript 최소화


전송되는 데이터의 용량을 줄이기 위해 CSS, Javascript 파일을 최소화하여 통신합니다.

 

 

# HTTP 2.0의 등장


HTTP 2.0은 HTTP를 아예 새롭게 개선하기 보다는 기존 HTTP 1.1을 개선하는 방향에서, 성능 쪽에 초점을 맞춘 프로토콜로서 2015년 2월 표준으로 승인되었습니다.

HTTP 1.1의 여러 문제점으로 구글이 개발한 비표준 개방형 프로토콜 SPDY를 기반하였습니다.

 

## Multiplexed Streams


HTTP 1.1의 HTTP Pipelining 의 개선안으로 하나의 Connection으로 동시에 여러 개의 메세지를 주고 받을 수 있습니다.

또한 응답은 요청 순서에 상관없이 Stream으로 받기 때문에 HOL Blocking 도 발생하지 않습니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/HTTP/%232.png"/>

## Stream Prioritization


응답에 대한 우선순위를 정해 우선순위가 높을수록 응답을 빨리 합니다.

예를 들어 하나의 HTML 문서에 CSS 파일과 여러 IMG 파일이 있다고 가정해봅시다.

만일 여러 IMG 파일을 응답하느라 CSS 파일의 응답이 느려지면 클라이언트는 렌더링을 하지 못하고 기다리게 됩니다.

따라서 CSS 파일의 우선순위를 올려 렌더링을 진행하며 IMG 파일은 도착하는 대로 띄어준다면 더 효율적입니다.

 

## Server Push


서버가 클라이언트의 요청없이 응답을 보내는 방법입니다.

위와 마찬가지로 하나의 HTML 문서에 CSS 파일과 여러 IMG 파일이 있다고 가정해봅시다.

기존에는 HTML 문서를 요청한 후 다시 각각의 CSS 파일과 여러 IMG 파일을 위한 요청을 보내야 했습니다.

하지만 Server Push 로 인해서 클라이언트의 요청을 최소화하고 서버가 리소스를 알아서 보내줍니다.

 

## Header Compression


HTTP 1.1의 경우 이전 요청과 중복되는 Header도 똑같이 전송하느라 네트워크 자원을 불필요하게 낭비하였습니다.

HTTP 2.0의 경우, Header Table과 Huffman Encoding을 사용하는 HPACK 압축방식으로 이를 개선하였습니다.

클라이언트와 서버는 각각 Header Table을 관리하고 이전 요청과 동일한 필드는 table의 index만 보내고, 변경되는 값은 Huffman Encoding 후 보냄으로서 Header의 크기를 경령화 하였습니다.

<img src="https://github.com/douzoneStudy/Web/blob/main/Images/HTTP/%233.png"/>


# Summary

## Http 1.0

- Request마다 새로운 Connection 필요

- 3-way-handshake가 매번 필요했기 때문에 성능적인 이슈 발생



## Http 1.1

- HTTP 1.0의 커넥션 문제를 해결하기함

 Keep Alive를 통해 해결

- 또한 Pipelining의 도입으로 여러 요청을 순차적으로 보내어 응답시간 감소.

다만, 앞선 요청의 응답이 느려지면 다음 요청의 응답 또한 늦어진다는 단점

(Head-of-Line blocking)





## Http 2.0

- 기존의 단점들을 다 커버하기 위해 고안됨 ( 기존 HTTP 표준을 바꾸는게 아닌 확장 )

- 먼저 전송방식이 변경됨( 기존방식은 텍스트 형식이었으나, HTTP 2.0부터는 바이너리로 바뀜)

- 프레임은 바이너리 형식으로 이루어져있으며

스트림안에서 양방향 전송이 가능하다.

- 이때문에 기존의 HTTP 1.1의 Head-of-line blocking 문제가 해결되었다.

- 먼저 오는 쪽의 데이터를 수신 측에서 조립 한다음에 처리하여 보내주기 때문

- 이전에 HTTP 1.1에서의 문제 중 하나가 헤더의 중복이었다. 이부분을

HTTP 2.0에서는 Static/Dynamic Header Table을 활용하여 중복 제거를 하였구요

- 또한 서버푸시 기능도 제공된다.

클라이언트가 리소스 요청하기 전에 먼저 판단해서 보내줌


- 마지막으로 HTTP 2.9에서는 리소스간의 전송 우선순위도 설정이 가능하다!

