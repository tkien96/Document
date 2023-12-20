# Redux - Saga
## Action
Action là một đối tượng đơn giản chứa thông tin về một sự kiện xảy ra trong ứng dụng. Action được tạo bởi các thành phần React hoặc các thư viện khác, và được dispatch (gửi) tới redux store.

Action đóng vai trò là cầu nối giữa các thành phần React và redux store. Chúng cung cấp thông tin về một sự kiện xảy ra trong ứng dụng, chẳng hạn như người dùng click vào một nút, tải một trang mới, hoặc nhập một giá trị.

```JS
import { createActions, createAction } from 'redux-actions';

export const getType = (reduxAction) => {
    return reduxAction().type;
};

export const getPosts = createActions({
    getPostsRequest: undefined,
    getPostsSuccess: (payload) => payload,
    getPostsFailure: (err) => err,
});

```
## Saga
Saga là một hàm generator không đồng bộ được sử dụng để xử lý các side effect trong ứng dụng. Side effect là bất kỳ hành động nào không thể được mô tả đơn giản bằng cách cập nhật trạng thái của redux store. Ví dụ: truy cập cơ sở dữ liệu, tải dữ liệu từ internet, hoặc thực hiện các thao tác mạng.

Saga đóng vai trò là một cách để xử lý các side effect trong ứng dụng một cách rõ ràng và dễ kiểm tra. Sagas có thể được sử dụng để truy cập cơ sở dữ liệu, tải dữ liệu từ internet, hoặc thực hiện các thao tác mạng.

```JS
import { takeLatest, call, put } from 'redux-saga/effects';
import * as actions from '../actions';
import * as api from '../../api';

function* fetchPostsSaga(action) {
  try {
    const posts = yield call(api.fetchPosts);
    yield put(actions.getPosts.getPostsSuccess(posts.data));
  } catch (err) {
    console.error(err);
    yield put(actions.getPosts.getPostsFailure(err));
  }
}

```
## Reducers
Reducer là một hàm được sử dụng để cập nhật trạng thái của redux store. Reducer được gọi mỗi khi một action được dispatch, và được cung cấp action và trạng thái hiện tại của redux store làm đầu vào.

Reducer đóng vai trò là một cách để cập nhật trạng thái của redux store một cách rõ ràng và nhất quán. Reducers có thể được sử dụng để cập nhật trạng thái của redux store dựa trên các action được dispatch.

```JS
// reducers/index.js
import { combineReducers } from 'redux';
import posts from './posts';
import modal from './modal';

export default combineReducers({
  posts,
  modal,
});

// reducers/post.js
import { INIT_STATE } from '../../constant';
import { getPosts, getType, createPost, updatePost } from '../actions';

export default function postsReducers(state = INIT_STATE.posts, action) {
  switch (action.type) {
    case getType(getPosts.getPostsRequest):
      return {
        ...state,
        isLoading: true,
      };
    case getType(getPosts.getPostsSuccess):
      return {
        ...state,
        isLoading: false,
        data: action.payload,
      };
    case getType(getPosts.getPostsFailure):
      return {
        ...state,
        isLoading: false,
    };
    default:
      return state;
  }
}
```

## Mối quan hệ giữa action, saga và reducer
Action, saga và reducer có mối quan hệ chặt chẽ với nhau trong mô hình redux-saga. Action được sử dụng để khởi tạo các saga, và saga được sử dụng để xử lý các side effect và cập nhật trạng thái của redux store. Reducers được sử dụng để cập nhật trạng thái của redux store dựa trên các action được dispatch.

Dưới đây là một ví dụ về cách sử dụng action, saga và reducer trong mô hình redux-saga:

```JS
// Action
const ACTION_TYPE_FETCH_POSTS = "FETCH_POSTS";

// Saga
function* fetchPosts() {
  const posts = yield call(Api.fetchPosts);
  yield put({ type: ACTION_TYPE_FETCH_POSTS, posts });
}

// Reducer
function postsReducer(state = [], action) {
  switch (action.type) {
    case ACTION_TYPE_FETCH_POSTS:
      return action.posts;
    default:
      return state;
  }
}

```

Trong ví dụ này, chúng ta có một action ACTION_TYPE_FETCH_POSTS được sử dụng để khởi tạo saga fetchPosts(). Saga fetchPosts() sẽ truy cập cơ sở dữ liệu để lấy danh sách các bài đăng, sau đó sử dụng put() để dispatch một action mới với danh sách các bài đăng. Reducer postsReducer() sẽ cập nhật trạng thái của redux store dựa trên action mới này.

Với mô hình redux-saga, chúng ta có thể xử lý các side effect trong ứng dụng một cách rõ ràng và dễ kiểm tra. Sagas cung cấp một cách để tách biệt các side effect khỏi các thành phần React, giúp cho ứng dụng dễ dàng quản lý và bảo trì hơn.

## Selectors
selector là một hàm được sử dụng để truy cập trạng thái của redux store. Selectors có thể được sử dụng để lấy một phần trạng thái, hoặc để tính toán một giá trị dựa trên trạng thái.

Chức năng của selectors là cung cấp một cách để truy cập trạng thái của redux store một cách rõ ràng và dễ kiểm tra. Selectors có thể được sử dụng bởi các thành phần React, sagas, hoặc các thư viện khác.

Vai trò của selectors trong mô hình redux-saga là:

Tách biệt trạng thái khỏi các thành phần React và sagas. Selectors cung cấp một cách để truy cập trạng thái mà không cần phải biết cách trạng thái được lưu trữ trong redux store. Điều này giúp cho các thành phần React và sagas dễ dàng quản lý và bảo trì hơn.
Cải thiện khả năng tái sử dụng. Selectors có thể được tái sử dụng bởi nhiều thành phần React hoặc sagas. Điều này giúp giảm thiểu sự lặp lại mã và cải thiện khả năng bảo trì của ứng dụng.
Cải thiện hiệu suất. Selectors có thể được sử dụng để tính toán các giá trị dựa trên trạng thái. Điều này có thể giúp cải thiện hiệu suất của ứng dụng bằng cách giảm thiểu số lượng lần truy cập trạng thái.
Dưới đây là một ví dụ về cách sử dụng selectors:

```JS
export const postsState$ = (state) => state.posts.data;
export const modalState$ = (state) => state.modal;
```

![](https://images.viblo.asia/a2d5bb2b-32a1-4258-b534-eeffeb95e177.png)