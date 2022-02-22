## NextJs
nextjs는 React의 SSR(Server-Side Rendering)을 쉽게 구현할 수 있게 도와주는 프레임워크이다. React만으로도 SSR을 자체적으로도 구현할 수 있긴 하지만 개발환경 세팅이 굉장히 복잡하다. 그래서 NextJs가 나왔고 간단한 설정만으로도 사용할 수 있다.

## NextJs 특징
#### 1. 직관적인 라우팅 시스템 (dynamic routes 지원)
![](https://images.velog.io/images/endol007/post/cbeeb297-3b96-48e0-ba5c-767bf7c13b71/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-11-17%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.49.49.png) <br/>
이렇게 pages폴더안에 페이지 컴포넌트를 생성하는 것으로 각각 /board, /addboard, /detail/:boardId 로 라우팅처리를 할 수 있고 폴더 구조로 명확하게 볼 수 있기 때문에 직관적이다.
#### 2. 빠른 페이지 로드를 위한 자동 코드 스플리팅
일반적인 React의 경우 페이지 규모가 커지고 초기 렌더링 때 모든 컴포넌트를 한번에 내려받게 되면 로딩속도가 느려질 수 있기 때문에 lazy-suspense나 loadable 라이브러리 등을 이용해서 각 페이지 컴포넌트 별로 따로 설정을 해서 코드분할을 해야한다. 하지만 NextJs의 경우 각 페이지별 코드분할이 기본으로 세팅되어 있어서 첫 렌더링시에 index페이지 하나만 불러오고 다른 페이지로 이동할 때에 해당 페이지만 불러온다.
#### 3. Pre-rendering, SSG(Static Generation) 과 SSR(Server-Side Rendering)을 각 페이지마다 지원
각 페이지 용도 별로 SSR과 SSG를 설정할 수 있다. 사이트에 페이지가 2개가 있다고 가정하면, 하나는 매번 요청시마다 새로 데이터를 가지고 프리렌더링해서 주고 다른 한 페이지는 빌드 시 최초 한번만 Pre-rendering을 해서 미리 index.html을 가지고 있다가 바로 클라이언트로 넘겨주도록 만들 수 있다.


## Pre-Rendering
Next.js는 기본적으로 모든 페이지를 pre-render한다. 즉, Next.js는 각 페이지마다 HTML파일을 만들어낸다는 것이다. 그리고 생성된 각각의 HTML페이지는 그에 맞는 Javascript파일과 상호작용하며 렌더링 된다. 이러한 과정을 Hydration이라고 부른다.
![](https://images.velog.io/images/endol007/post/12076c91-9bb7-4a0e-bdb7-6c2e4bd05955/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-11-17%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.32.50.png)

### Pre-Rednering의 두가지 타입

- Static Generation(SSG) - HTML파일이 최초 빌드시 생성되고 매 요청마다 HTML파일이 재사용된다
- Server-side Rendering(SSR) - 매 요청마다 새로운 HTML파일을 프리렌더링한다.

Next.js에서는 성능상의 이점으로는 SSG를 사용할 것을 권장하고 있지만 각 페이지의 용도와 목적에 따라 SSG와 SSR을 사용하면 된다.

### Static Generation
Static Generation을 이용하면 HTML페이지가 최초 빌드시에 생성된다. (revalidate를 이용해서 최초 빌드뿐 아니라 특정 시간이 지날때마다 새로 HTML페이지를 갱신할 수 있다.) 즉, next buil로 빌드를 하게 되면 HTML페이지가 생성되고 이 생성된 페이지는 매요청마다 재사용된다. 그리고 이것은 CDN에 의해 캐싱되어 저장 될 수도 있다.
#### SSG without data
~~~javascript
const test = () => {
	return <div>test</div>
}

export default test
~~~
이런식으로 데이터 없이 구현을 하면 기본적으로 위 페이지가 HTML페이지로 생성된다.
#### SSG with data
~~~javascript
export const getStaticProps = async () => {
	const res = await fetch('http://localhost/boards');
	const boards: BoardType[] = await res.json();
	
	return {
		props:{
			boards,
		},
	}
}

const test = ({boards}: InferGetStaticPropsType<typeof getStaticProps>) => {
	return boards.map((item: BoardType) => {
		return <p key={item.id}>{item.comment}</p>;
	});
};

export default test;
~~~
이처럼 데이터를 fetch해서 합친 상태로 HTML페이지가 생성되어서 오게 된다. 그리고 getStaticPaths는 동적 라우팅이 필요할 때에 쓰면 된다.

그래서 Static Generation은 어느 상황에 쓰는게 효율적일까?
데이터를 최신상태로 유지할 필요가 없는 페이지들에 쓰는게 효율적일것이다. 그 예로 마케팅 페이지 혹은 홍보페이지, 그리고 블로그 게시글이나 포트폴리오 등이 있다.

### Server-Side Rendering
Server-side Rendering을 이용하면 SSG와는 다르게 매 요청마다 새로운 HTML페이지를 생성해서 보내준다.
구현하는 방식은 getStaticProps와 비슷하다.
~~~javascript
export const getServerSideProps = async () => {
	const res = await fetch('http://localhost/boards');
	const boards: BoardType[] = await res.json();
	
	return {
		props:{
			boards,
		},
	}
}

const test = ({boards}: InferGetServerSidePropsType<typeof getServerSideProps>) => {
	return boards.map((item: BoardType) => {
		return <p key={item.id}>{item.comment}</p>;
	});
};

export default test;
~~~
이렇게 구현을 하면 매 요청마다 미리 data를 fetch해서 프리렌더링 한 상태로 HTML페이지를 브라우저로 보내게 된다.

### Hydrate
 Next.js의 동작원리에서 빼놓을 수 없는 중요한 용어이다. 중요한 개념이지만 이 원리를 몰라도 구현만 하는데에는 문제가 없기 때문에 놓치기 쉬운 개념이다.
 Hydrate는 ServerSide에서 렌더링된 정적 HTML페이지와 번들링된 JS파일을 클라이언트에 보낸 뒤 클라이언트(브라우저)에서 HTML코드와 React인 JS코드를 서로 매칭 시키는 과정을 말한다.
 
![](https://images.velog.io/images/endol007/post/4cd3b2db-23f1-4c04-9815-2eb40c6da59a/image.png)

기존의 React에서는 빈 껍데기의 HTML문서와 JS파일을 모두 클라이언트로 보낸 뒤 클라이언트에서 JS코드를 통해 화면을 렌더링 하며 페이지를 그린다. 그 이후 유저와 상호작용하는 모든 이벤트들이 JS로 동작한다.
~~~html
<div id="root"></div>
~~~

Next.js에서는 클라이언트에 웹 페이지를 보내기 전에 미리 Server-side에서 웹 페이지를 pre-rendering하고 이 생성된 HTML페이지를 클라이언트에 전송한다. 
하지만 이 pre-rendering된 HTML페이지는 단순히 화면만 보여주는 상태이고 JS요소들이 들어가 있지 않는 상태이다. 그리고 그 이후 리액트가 바로 번들링 된 JS파일을 클라이언트로 전송한다.
![](https://images.velog.io/images/endol007/post/7f0455df-4ff2-4270-be95-5be31e273802/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202021-11-18%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%205.00.27.png)
네트워크를 확인해보면 board라는 pre-rendering된 HTML문서가 넘어와있다.
그리고 그이후 밑의 번들링된 js파일들이 board라는 HTML문서위에 한번더 렌더링을 하면서 각자 자리를 찾아가며 매칭되는 과정을 Hydrate라고 한다.


#### Next-redux-wrapper
Next.js에서도 redux를 쓰는 경우가 많을텐데 이 경우에 이용하게 되는 라이브러리가 next-redux-wrapper이다. Reducer에서 Hydrate를 처리해주는 부분이 있다. 이는 Server-Side에서 dispatch하고 사용했던 state들을 Client단에서도 그대로 이용할 수 있도록 Hydration이 필요하기 때문이다. 
즉, 서버 단에서 redux state와 client단의 state가 서로 다르면 안되기 때문에 동기화를 시켜주는 개념이라도 봐도 될 것 같다.
~~~javascript
export const getServerSideProps: GetServerSideProps = wrapper.getServerSideProps(
	store => async () => {
		store.dispatch(boardDataActions.getBoards());
		store.dispatch(END);
		await store.sagaTask?.toPromise();
		return { props: {} };
	},
);
~~~
위와 같이 server-side단에서 dispatch를 하고 state를 받을 경우 hydration을 통해 데이터를 맞춰주는 작업이 필요한 것이다.
~~~javascript
const boardSlice = createSlice({
  name: 'board',
  initialState,
  reducers: {...},
  extraReducers: {
    [HYDRATE]: (state, action) => {
      console.log(action.payload)
      return action.payload.board;
    },
  }
});
~~~
그래서 reducer의 액션들 밑에 HYDRATE액션을 통해 client단의 state를 최신화 시키는 작업을 해주면 정상적으로 사용을 할 수 있다.

- hydration: 수화란 물에 용해된 용질분자나 이온을 물 분자가 둘러싸 상호작용하여 하나의 분자처럼 되는 것을 뜻한다.

hydration의 뜻처럼 결국 서버와 클라이언트의 상태를 하나의 상태처럼 만드는 것을 hydration이라고 하는 것 같다.
