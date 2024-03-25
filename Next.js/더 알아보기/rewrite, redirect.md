
#### 1. rewrite

- Next.js에서 제공하는 `rewrite` 기능은 사용자가 특정 `path`로 이동 시, 정해진 화면이 보이도록 한다.

- 사용자가 입력한 **URL 자체는 그대로 보여지지만**, 실제 페이지는 **다른 페이지의 내용을 출력하도록 구현**된다.
	- 즉, 대상 경로를 숨기고 사용자가 사이트에서의 위치를 변경하지 않은 것처럼 **URL 프록시** 역할을 한다.
		- **URL 프록시** : 클라이언트와 서버 간의 통신에서 중간에 위치하여 클라이언트로부터의 요청을 받아서 서버로 전달하고, 서버로부터의 응답을 클라이언트로 전달해주는 서비스이다. 이는 클라이언트가 직접 서버와 통신하지 않고 중간 프록시를 통해 통신하게 되는 구조를 말한다.
			1. **보안 강화**: 클라이언트와 직접 통신하지 않고 중간에 프록시를 두면 서버의 실제 IP 주소를 숨길 수 있어 보안을 강화할 수 있다.
			2. **캐싱**: 프록시는 이전에 요청된 리소스를 캐시하여 동일한 요청이 들어왔을 때 서버에 요청을 보내지 않고 캐시된 데이터를 반환함으로써 네트워크 성능을 향상시킬 수 있다.
			3. **로드 밸런싱**: 다수의 서버에 요청을 분산하는 로드 밸런싱을 수행하여 서버의 부하를 분산할 수 있다.
			4. **사용자 지역 기반 라우팅**: 프록시는 사용자의 위치를 파악하고 해당 위치에 가장 가까운 서버로 요청을 전달하여 지역별 서비스를 제공할 수 있다.
			5. **서비스 접근 제어**: 특정 사용자나 IP 주소에서의 요청을 필터링하거나 차단하여 보안을 강화하거나 접근 권한을 관리할 수 있다.

- 속성은 아래와 같다.
	- `source`: 들어오는 요청 경로 패턴을 나타낸다.
	- `destination`: 해당 요청이 라우팅되어야 하는 대상 경로를 나타낸다.
	- `basePath`: 주로 외부 리라이팅에 사용되며, 기본 경로를 설정하는 속성이다. 이 값을 false로 설정하면 `basePath`가 일치할 때 포함되지 않는다. 
	- `locale`: `locale`을 설정한다. `false` 또는 `undefined`로 설정하면 매칭 시 `locale`이 포함되지 않는다.
	- `has`: 요소에 대한 정보를 담은 객체의 배열이다. 각 객체는 `type`, `key`, `value` 속성을 가지고 있다.
	- `missing`: 부재하는 요소에 대한 정보를 담은 객체의 배열이다. 각 객체는 `type`, `key`, `value` 속성을 가지고 있다.

##### 사용법

- 기본적인 사용법은 아래와 같다. (`next.config.js`)
```js
module.exports = {
  // rewrite
  async rewrites() {
    return [
      {
        // source : 유저가 진입할 path
        // destination : 유저가 이동할 path
        source: '/about',
        destination: '/',
      },
    ]
  },
}
```

-   `rewrites` 함수가 배열을 반환할 때, `rewrites`는 파일 시스템 (`pages` 및 `/public` 파일)을 확인한 후 동적 경로 이전에 적용된다고 한다. 
	- 그러나 rewrites 함수가 특정 형태의 배열을 가진 객체를 반환하면, 이 동작을 변경하고 더 정교하게 제어할 수 있는데. 이는 Next.js의 v10.1부터 지원된다고 한다.
	
	```js
module.exports = {
  async rewrites() {
	return {
	  beforeFiles: [
		// These rewrites are checked after headers/redirects
		// and before all files including _next/public files which
		// allows overriding page files
		{
		  source: '/some-page',
		  destination: '/somewhere-else',
		  has: [{ type: 'query', key: 'overrideMe' }],
		},
	  ],
	  afterFiles: [
		// These rewrites are checked after pages/public files
		// are checked but before dynamic routes
		{
		  source: '/non-existent',
		  destination: '/somewhere-else',
		},
	  ],
	  fallback: [
		// These rewrites are checked after both pages/public files
		// and dynamic routes are checked
		{
		  source: '/:path*',
		  destination: `https://my-old-site.com/:path*`,
		},
	  ],
	}
  },
}
	```

##### Rewrite parameters (rewriting 매개변수)

1. `rewrites`에서 매개변수 사용 시, `destination`에 매개변수가 사용되지 않았다면, 기본적으로 쿼리에 매개변수가 전달된다.
	- 예를 들어, `/old-about/:path*`와 같은 `source`를 `/about`로 `rewrites`할 때, `destination`에는 `:path` 매개변수가 사용되지 않기 때문에 자동으로 쿼리에 매개변수가 전달된다.

	- [요청] : 만약, 사용자가 `/old-about/some-page` 경로로 요청을 보낸다.
	- [결과] :  `source`경로는 `/old-about/:path*`이다. 이는 `/old-about/` 다음에 오는 모든 것을 `:path` 매개변수로 인식하도록 한다.
		- `destination` 경로는 `/about`이다. 이 경우, `destination` 경로에는 `:path` 매개변수가 사용되지 않았다.
		- 따라서, `/old-about/some-page` 경로의 요청에서 `:path` 매개변수인 `some-page`는 자동으로 쿼리(query)에 전달된다.
		- 결론적으로, 위의 설정은 `/about?path=some-page`와 같이 요청을 처리한다.
```js
module.exports = {
  async rewrites() {
    return [
      {
        source: '/old-about/:path*',
        
        // The :path parameter isn't used here so will be automatically passed in the query
        destination: '/about', 
      },
    ]
  },
}
```

2. 그러나 대상에 매개변수가 사용되면, 쿼리에는 매개변수가 자동으로 전달되지 않는다.
	- 예를 들어, `/docs/:path*`와 같은 `source`를 `/:path*`로 `rewrites`할 때, 대상에는 `:path` 매개변수가 사용되므로 쿼리에 매개변수가 자동으로 전달되지 않는다.

	- [요청] : 만약, 사용자가 `/docs/some-page` 경로로 요청을 보낸다.
	- [결과] : `source` 경로는 `/docs/:path*`이다. 이는 `/docs/` 다음에 오는 모든 것을 `:path` 매개변수로 인식한다.
		- `destination` 경로는 `/:path*`이다. 이 경우, `destination`경로에는 `:path` 매개변수가 사용되었다.
		- `destination` 경로에 매개변수가 사용되었으므로, 쿼리(query)에 매개변수가 자동으로 전달되지 않는다.
		- 결론적으로, `/some-page`와 같이 요청을 처리한다.
```js
module.exports = {
  async rewrites() {
    return [
      {
        source: '/docs/:path*',

		// The :path parameter is used here so will not be automatically passed in the query
        destination: '/:path*', 
      },
    ]
  },
}
```

3. 만약 `destination`경로에 이미 매개변수가 사용되고 있다면, 명시적으로 `destination`에 쿼리를 지정하여 매개변수를 수동으로 전달할 수 있다. 이것은 매개변수가 중복되는 상황에서도 쿼리에 추가적인 매개변수를 전달하고자 할 때 사용된다.
	- [요청] :  만약, 사용자가 `/apple/banana`로 요청을 보낸다.
	- [결과] :  `source` 경로는 `/:first/:second`이다. 이는 첫 번째 매개변수인 `:first`와 두 번째 매개변수인 `:second`를 포함한다.
		- `destination`경로는 `/:first?second=:second`이다. 이 경우, `:first` 매개변수가 이미 `destination` 경로에 사용되었으니, `:second` 매개변수는 쿼리(query)에 자동으로 추가되지 않는다.
		- 따라서, `/apple/banana` 경로의 요청에서 `:second` 매개변수를 전달하려면 명시적으로 `destination`에 쿼리를 지정해야 한다.
		- 결론적으로, 위의 설정은 `/apple/banana?second=banana`와 같이 요청을 처리한다.
```js
module.exports = {
  async rewrites() {
    return [
      {
		source: '/:first/:second', 
		destination: '/:first?second=:second',
	  },
    ]
  },
}
```

##### Path Matching (경로 일치)

- 경로 일치는 `/blog/:slug`와 같은 매개변수를 사용하는 경로 패턴을 허용한다.
	- 이것은 `/blog/hello-world`와 같은 경로를 의미하는데, **이것은 중첩된 경로는 허용하지 않으며,** 대신 동적인 `path`값을 받아오기에 유용하다

- `/blog/apple` -> `/news/apple` (o)
- `/blog/banana` -> `/news/banana` (o)
- `/blog/apple/green` (x)
```js
module.exports = {
  async rewrites() {
    return [
      {
        source: '/blog/:slug',
        destination: '/news/:slug', // Matched parameters can be used in the destination
      },
    ]
  },
}

```
##### Wildcard Path Matching (와일드카드 경로 일치)

- 와일드카드 경로를 일치시키려면 매개변수 뒤에 `*`를 사용할 수 있다.
	- 예를 들어, `/blog/:slug*`는` /blog/a/b/c/d/hello-world`와 같은 경로와 일치한다.
	- 이것은 **중첩된 경로를 허용한다.**

- `/blog/apple/green` -> `/news/apple/green` (o)
- `/blog/apple/green/delicious` -> `/news/apple/green/delicious` (o)
```js
module.exports = {
  async rewrites() {
    return [
      {
        source: '/blog/:slug*',
        destination: '/news/:slug*', // Matched parameters can be used in the destination
      },
    ]
  },
}
```
##### Regex Path Matching (정규식 경로 일치)

- 정규식 경로를 일치시키려면 매개변수 뒤에 정규식을 괄호로 감싸면 된다. 
	- 예를 들어, `/blog/:slug(\d{1,})`은 `/blog/123`과 같은 경로와 일치하지만, `/blog/abc`와 같은 경로와는 일치하지 않는다.
```js
module.exports = {
  async rewrites() {
    return [
      {
	    // `(\\d{1,})`에서, `\d`는 숫자를 나타내고 `{1,}`는 최소한 한 자리 이상의 숫자를 나타낸다.
        source: '/old-blog/:post(\\d{1,})',
        destination: '/blog/:post', // Matched parameters can be used in the destination
      },
    ]
  },
}
```


#### 2. redirect

- Next.js에서 제공하는 `redirect` 기능 또한 사용자가 특정 `path`로 이동 시, 정해진 화면이 보이도록 한다.

- 다만, **주소창의 URL 자체가 이동하고자 하는 페이지의 URL로 변경**되어 이동하는 점이 `rewrite`와의 차이이다.

- 속성은 아래와 같다.
	- `source`: 들어오는 요청 경로 패턴을 나타낸다.
	- `destination`: 해당 요청의 라우팅할 경로를 나타낸다.
	- `permanent`: `true` or `false`의 값을 가지며, `redirection`에만 존재하는 속성값이다. 사용자나 검색 엔진에서 해당 `redirect` 값을 영구적으로 저장할 것인지에 대한 여부를 지정한다. 만약 이벤트 페이지 이거나 임시 페이지인 경우는 `false`로 지정해주면 된다.
		- `true`: 308 상태 코드를 사용하여 클라이언트/검색 엔진에게 리디렉션을 영구적으로 캐시할 것을 지시한다.
		- `false`: 307 상태 코드를 사용하여 일시적이며 캐시되지 않는다.
	- `basePath`: `false` or `undefined`의 값을 가진다. `false`는 `basePath`가 일치할 때 포함되지 않으며, 외부 `redirect`에만 사용할 수 있음을 의미한다.
	- `locale`: `false` or `undefined`의 값을 가진다. `locale`이 일치할 때 포함해야 하는지 여부이다.
	- `has`:  `type`, `key`, `value` 속성을 가진 `has` 객체의 배열이다.
	- `missing`: 유형, 키 및 값 속성을 가진 `missing`객체의 배열이다. `redirect`은 파일 시스템 (`pages` 및 `/public` 파일) 이전 단계에서 확인된다.

##### 사용법

- ㅋㅋ
```js
module.exports = {
// source path에 적용 여부를 나타낸다. 
// `false`는 destination에 외부 링크가 사용된 경우에만 사용할 수 있는 속성이다.
  basePath: '/docs',

  async redirects() {
    return [
      {
        source: '/with-basePath', 
        destination: '/another',
        permanent: false,
      },
      {
        source: '/without-basePath',
        destination: 'https://example.com',
        basePath: false,
        permanent: false,
      },
    ]
  },
}
```

##### Path Matching (경로일치)

##### Wildcard Path Matching (와일드카드 경로 일치)

##### Regex Path Matching (정규식 경로 일치)