# CRWN CLOTHING REDUX-TOOLKIT

## 1. 소개 및 참여 인원
- 쇼핑몰을 구성하고, 로그인, 장바구니 추가, 구매기능 구현 예정
- 개인 프로젝트

## 2. 사용 기술
#### `Front-end`
![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/css3-%231572B6.svg?style=for-the-badge&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)
![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![React Router](https://img.shields.io/badge/React_Router-CA4245?style=for-the-badge&logo=react-router&logoColor=white)
![SASS](https://img.shields.io/badge/SASS-hotpink.svg?style=for-the-badge&logo=SASS&logoColor=white)
![Firebase](https://img.shields.io/badge/firebase-a08021?style=for-the-badge&logo=firebase&logoColor=ffcd34)
![Styled Components](https://img.shields.io/badge/styled--components-DB7093?style=for-the-badge&logo=styled-components&logoColor=white)
![Redux](https://img.shields.io/badge/redux-%23593d88.svg?style=for-the-badge&logo=redux&logoColor=white)

- cart.reducer.js
- addCartItem
- 이미 장바구니에 있는 상품이라면, 해당 상품의 quantity를 증가시킵니다.
- 장바구니에 없는 상품이라면, 상품을 추가하고 기본 quantity를 1로 설정합니다.
```
const addCartItem = (cartItems, productToAdd) => {
  const existingCartItem = cartItems.find(
    (cartItem) => cartItem.id === productToAdd.id
  );

  if (existingCartItem) {
    return cartItems.map((cartItem) =>
      cartItem.id === productToAdd.id
        ? { ...cartItem, quantity: cartItem.quantity + 1 }
        : cartItem
    );
  }

  return [...cartItems, { ...productToAdd, quantity: 1 }];
};
```

- removeCartItem
- 장바구니에서 지정된 상품의 quantity를 감소시킵니다.
- quantity가 1이면 해당 상품을 장바구니에서 제거합니다.
```
const removeCartItem = (cartItems, cartItemToRemove) => {
  const existingCartItem = cartItems.find(
    (cartItem) => cartItem.id === cartItemToRemove.id
  );

  if (existingCartItem.quantity === 1) {
    return cartItems.filter((cartItem) => cartItem.id !== cartItemToRemove.id);
  }

  return cartItems.map((cartItem) =>
    cartItem.id === cartItemToRemove.id
      ? { ...cartItem, quantity: cartItem.quantity - 1 }
      : cartItem
  );
};
```

- clearCartItem
- 지정된 상품을 장바구니에서 완전히 제거합니다
```
const clearCartItem = (cartItems, cartItemToClear) =>
  cartItems.filter((cartItem) => cartItem.id !== cartItemToClear.id);
```

- 초기상태
- isCartOpen: 장바구니 UI 열림 여부를 저장.
- cartItems: 장바구니 항목의 배열.
```
const CART_INITIAL_STATE = {
  isCartOpen: false,
  cartItems: [],
};
```

- cartSlice 정의
- createSlice를 사용하여 Redux 슬라이스를 만듭니다. 이 슬라이스는 상태와 리듀서, 그리고 액션 크리에이터를 포함합니다.
  
- reducers (액션 정의)
- setIsCartOpen: 장바구니 UI의 열림 상태를 설정합니다.
- addItemToCart: 상품을 장바구니에 추가합니다 (addCartItem 호출).
- removeItemFromCart: 상품 수량을 감소하거나 제거합니다 (removeCartItem 호출).
- clearItemFromCart: 상품을 완전히 제거합니다 (clearCartItem 호출).
```
export const cartSlice = createSlice({
  name: 'cart',
  initialState: CART_INITIAL_STATE,
  reducers: {
    setIsCartOpen(state, action) {
      state.isCartOpen = action.payload;
    },
    addItemToCart(state, action) {
      state.cartItems = addCartItem(state.cartItems, action.payload);
    },
    removeItemFromCart(state, action) {
      state.cartItems = removeCartItem(state.cartItems, action.payload);
    },
    clearItemFromCart(state, action) {
      state.cartItems = clearCartItem(state.cartItems, action.payload);
    },
  },
});
```

- 액션 및 리듀서 내보내기
- cartSlice.actions를 통해 액션 생성기를 내보냅니다.
- cartSlice.reducer는 상태를 업데이트하기 위한 리듀서로 사용됩니다.
```
export const {
  setIsCartOpen,
  addItemToCart,
  removeItemFromCart,
  clearItemFromCart,
} = cartSlice.actions;

export const cartReducer = cartSlice.reducer;
```
- Redux Toolkit의 immer 덕분에 불변성을 신경 쓰지 않고 직관적으로 상태를 업데이트할 수 있습니다.
  
- user.reducer.js
- 초기 상태 (INITIAL_STATE)
- 의미: 사용자 상태의 기본값을 정의합니다.
- currentUser: 현재 로그인된 사용자의 정보를 저장합니다. 초기값은 null로, 로그인된 사용자가 없음을 나타냅니다.
```
const INITIAL_STATE = {
  currentUser: null,
};
```

- createSlice로 슬라이스 생성
- createSlice는 상태와 리듀서를 결합하고, 자동으로 액션 생성기를 만들어 줍니다.
- name: 슬라이스의 이름을 정의합니다. 이 이름은 디버깅 또는 Redux DevTools에서 액션 타입으로 표시됩니다 (user/setCurrentUser 등).
- initialState: 초기 상태를 지정합니다.
- reducers: 상태를 변경하는 액션과 해당 로직을 정의합니다.
```
export const userSlice = createSlice({
  name: 'user', // 슬라이스 이름
  initialState: INITIAL_STATE, // 초기 상태
  reducers: {
    setCurrentUser(state, action) { // 사용자 정보를 설정하는 액션
      state.currentUser = action.payload; // 상태를 업데이트
    },
  },
});
```
- reducers 내부 동작
- setCurrentUser
- 기능: currentUser 값을 업데이트합니다.
- action.payload: 사용자 정보 (예: 로그인 성공 시 반환된 사용자 객체).

- 액션 및 리듀서 내보내기
- setCurrentUser: 액션 생성기.
- 이 함수를 호출하면 type과 payload를 포함하는 액션 객체가 반환됩니다.
- userReducer: 이 슬라이스와 연결된 리듀서 함수.
```
export const { setCurrentUser } = userSlice.actions;
export const userReducer = userSlice.reducer;
```
- 이 코드는 사용자의 로그인 상태와 정보를 Redux 상태로 관리하기 위한 기본적인 구조를 제공합니다. 이를 통해 로그인, 로그아웃, 사용자 정보 업데이트와 같은 작업을 효율적으로 처리할 수 있습니다.

- categories.reducer.js
- 초기 상태 (CATEGORIES_INITIAL_STATE)
- 의미: 카테고리 데이터의 초기 상태를 정의합니다.
- categories: 카테고리 정보를 저장하는 배열이며, 초기값은 빈 배열([])입니다.
```
export const CATEGORIES_INITIAL_STATE = {
  categories: [],
};
```

- createSlice로 슬라이스 생성
- createSlice를 사용하여 상태, 액션, 리듀서를 정의합니다.
- name: 슬라이스의 이름으로, 액션 타입의 접두사로 사용됩니다 (예: categories/setCategories).
- initialState: 초기 상태를 설정합니다.
- reducers: 상태를 변경하는 액션과 해당 로직을 정의합니다.
```
export const categoriesSlice = createSlice({
  name: 'categories', // 슬라이스 이름
  initialState: CATEGORIES_INITIAL_STATE, // 초기 상태
  reducers: {
    setCategories(state, action) { // 카테고리 정보를 설정하는 액션
      state.categories = action.payload; // 상태 업데이트
    },
  },
});
```
- setCategories 액션
- 기능: categories 배열을 새로운 값으로 설정합니다.
- action.payload: 새로운 카테고리 배열 데이터를 받아서 categories 상태를 갱신합니다.

- 액션 및 리듀서 내보내기
- setCategories
- 액션 생성기로, 카테고리 데이터를 업데이트할 때 사용됩니다.
- categoriesReducer
- 슬라이스와 연결된 리듀서로, Redux 스토어에서 사용됩니다.
```
export const { setCategories } = categoriesSlice.actions;
export const categoriesReducer = categoriesSlice.reducer;
```
- 이 코드는 카테고리 데이터를 Redux 상태로 관리하기 위한 기본적인 구조를 제공합니다. setCategories를 통해 카테고리 데이터를 설정하거나 초기화할 수 있으며, 다른 상태와 쉽게 통합할 수 있는 확장성을 갖추고 있습니다.
