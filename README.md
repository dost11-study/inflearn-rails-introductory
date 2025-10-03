# inflearn-rails-introductory
인프런의 [[입문] 인디해커를 위한 루비온레일즈 8 입문 강의](https://www.inflearn.com/course/rubyonrails8-%ED%92%80%EC%8A%A4%ED%83%9D-%EC%9B%B9%EA%B0%9C%EB%B0%9C-%EC%9E%85%EB%AC%B8)를 정리한 내용입니다.

## 문법

- notice.present? -> nil, "", [], {} 는 전부 false
- 메서드명 끝에 붙는 !는 원본 객체를 변경한다는 의미의 강조지만, Active Record에서는 ActiveRecord::RecordInvalid 예외 발생 발생시킴
- rails는 return이 없으면 무조건 마지막 줄을 반환함
- 심볼의 경우 같은 글자의 심볼은 새로 생성되지 않고, 기존 객체를 찾아서 재사용함
  따라서 hash object에서는 문자열이 아니라 심볼을 사용해야함 `person[:name]`
  심볼키를 사용할 경우 `person = { name: "James" }`이지만 문자열 키(`person["name"]`)로 가져오고 싶다면 `person = { "name" => "James" }`를 사용해야함, 이때 => 는 해시 로켓이라고 별칭함
- `def adult?(age)`와 같이 함수명 마지막에 ?를 붙이는 경우는 boolean 타입을 반환한다고 가정할 수 있음 (필수 아니고 관례)
- do 키워드 대신`3.times { puts "안녕하세요!" }` 블록 형태로도 자주 사용함
- `fruits.map(&:length)` 는 루비의 단축 문법인데 &는 심볼을 블록으로 변환하는 기능이 있고, :length와 같은 메서드 이름을 심볼로도 사용 가능함
  Ruby 내부에서 메서드명은 심볼로 관리되기 때문이다
- `string&.split`와 같은 메서드는 뒤의 부분이 nil일 경우 아예 메서드 자체를 실행해버리지 않고 nil을 반환함
- Rails는 메서드 호출 시 괄호 생략이 가능하다
- `irb`를 통해 루비의 순수 인터프리터 사용이 가능하다

## 라우팅

- RESTful 한 리소스 라우팅은 굳이 하나씩 나열해서 적을 필요 없이 `resources :posts`와 같이 라우팅 생성이 가능하다 (posts_path, post_path(post) 등 라우팅 헬퍼 메서드들도 자동으로 생성해준다.)
  - 확인은 `rails routes`를 통해 생성된 라우팅과 라우팅 헬퍼 메서드 등을 확인할 수 있다

## 모델

- 데이터 & 비즈니스 로직 담당
- `rails g model Post title:string content:text`
- 모델은 단수 형태, 컨트롤러는 복수 형태, 테이블 명은 복수형 (관례)
- `Post.new`로 생성하고 `post.save` 하거나 `Post.create` 를 통해 저장 가능 (create는 객체 생성 및 저장 동시에 수행)

## 마이그레이션

- DB 스키마 변경 이력 관리
- 실서버에 배포할 때는 마이그레이션이 자동으로 실행이 되기 때문에 명령어 실행도 필요 없음
- `rails c`를 통해 레일즈 콘솔에서 Active Record 테스트 가능

## 뷰

- erb 태그중 <% %>가 아닌 등호가 있는 <%= %>는 화면에 출력이 됨
- `rails g controller posts`
- tailwind를 사용하고 있다면 `rails s` 대신 `./bin/dev`를 통해 실행해야함
- tailwind.config.js는 tailwind 설정 파일, application.tailwind.css는 기본 스타일 파일이다.

## Scaffold

- CRUD 자동 생성 (Model, View, Controller, Route, Migration 등)
  - `rails generate scaffold Post title:string content:text`

- 마이그레이션
  - `rails db:migrate`

- 마이그레이션이 수행되지 않은 상태에서 Schema 결과물 로드
  - `rails db:schema:load`

- 뷰에서 파샬 랜더링은 `<%= render 'shared/header' %>`처럼 가능하며, props와 같이 하위 파셜로 넘길 수 있음
  - <%= render partial: '파셜명',           # 파셜 파일 지정
        locals: { key: value },      # 지역 변수 전달 (고정 키워드)
        collection: @items,          # 컬렉션 렌더링
        as: :item,                   # 컬렉션 항목의 변수명 지정
        spacer_template: 'spacer',   # 항목 사이 구분자
        layout: 'wrapper' %>         # 파셜을 감쌀 레이아웃

- *<!-- 단축 문법 -->* 
  <%= render "posts/post", post: post, show_author: true %>
   *<!-- 위와 완전히 동일한 풀 문법 -->* 
  <%= render partial: "posts/post", locals: { post: post, show_author: true } %>


  위처럼 rails에서는 자체적으로 축약 문법을 제공하며, 마찬가지로 link_to 에서도 축약 가능

- Rails의 **RESTful 규칙**과 `form_with`의 **자동 판단 로직** 덕분에 new인지 edit인지 판단하여 hidden 태그를 사용하여 자동으로 판단하여 라우팅해줌
- 실제 영속화된 모델을 delete할 때는 destroy 사용함
- Json Jbuilder가 존재할 경우 json 응답을 보고 싶다면 URI Path에 X.json 즉 .json을 붙이면 확인 가능
- `rails test`를 통해 테스트 가능

## Hotwire

### Turbo Drive (기본 활성화)

- 페이지 전환 가속화 (prefatch처럼 AJAX를 통해 목록에 마우스만 올리면 미리 해당 html fetch 해옴)
- 전체 페이지 새로고침 X (SSR 이지만 전체 리프레시가 아니라 일부 dom만 업데이트)

### Turbo Frames (영역 교체)

- 페이지 일부만 부분적으로 업데이트 (turbo_frame_tag로 감싼 영역 기준)

### Turbo Streams (세밀한 DOM조작, 기존 내용 유지하면서 추가/삭제/수정)

- 실시간 업데이트 (replace, remove, update, before, after)
  - views 폴더에 xxxx.turbo_stream.erb 필요
- PubSub 사용으로 다른 모든 브라우저에 실시간 업데이트

### Stimulus

화면이 동적으로 바뀌어야 하는 (ex. Sidebar) 와 같은 간단한 자바스크립트에 사용

### Inertia-rails

일부 페이지에서 동적인 처리가 많이 필요할 경우 해당하는 페이지에다 React/Svelte/Vue 등 적용

https://github.com/inertiajs/inertia-rails

## Rails 타입 힌트

Sorbet, RBS(Ruby 3.0+), Steep과 같은 라이브러리를 통해 타입힌트 사용 가능

## Ruby 테스팅

RSpec을 사용해 테스트 코드 작성 가능

## Gem

Ruby나 Rails에서 사용하는 패키지 관리 시스템

- RubyGems.org: Gem 저장소
- Gemfile: Gem 목록 관리 (버전 등)
- bundle install: Gem 설치

## Rakefile

Ruby의 빌드 도구인 Rake의 설정 파일
Make의 Ruby 버전으로 작업(task)들을 정의하고 실행할 수 있게 해주는 도구

## Pagination

kaminari 혹은 pagy(성능 최고) 등을 사용해 간단하게 페이징 처리할 수 있음

