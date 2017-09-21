# vuex-playlist

> Vuex project demo

## Tại sao lại dùng Vuex
- Thông thường, khi truyền dữ liệu từ parent components xuống child components, ta sẽ bind data vào child component và lấy ra ở child components bằng  `props` hoặc dùng emit để hứng event, data truyền lên parent components.
- Khi Web app có cấu trúc lớn hơn, component nhiều nhưng lại dùng chung một số data, nếu sử dụng phương thức cũ thì code rất cồng kềnh và khó kiểm soát, do đó Vuex được dùng để tạo ra 1 store, nơi các component có thể dùng chung data trong đó.
## Demo Vuex
1. Tạo 1 folder `store`, tạo mới 1 file là `store.js` nơi lưu trữ thông tin chung của Vuex
2. <strong>`Computed`</strong>Các component lúc này không sử dụng phương thức `data` để hứng dữ liệu nữa mà dùng phương thức `computed`
- Ở file `store`, khai báo data bằng cách khai báo state:
```js
state: {
  products: [
    {name:'Banana skin',price: 20},
    {name:'Shiny sahr',price:40},
    {name:'Greeen shells', price: 60},
    {name: 'Red Shells', price: 80}
  ]
}
```
- Ở component cần nhận data:
```js
computed: {
  products () {
    return this.$store.state.products
  }
}
```
>products là tên của data cần nhận để render<br>
> this.$store : component sẽ truy cập vào store của  Vuex <br>
> .state.products : state và products là thuộc tính được khai báo trong store.

- Khi `$store.state` thay đổi, thuộc tính computed sẽ được tính toán lại và một trigger sẽ được tạo ra cho các DOM liên quan.

3.<strong>`Getters`</strong>: Trong trường hợp cần xử lý data/ data thuộc dạng thay đổi thường xuyên, thông thường sẽ dùng phương thức `computed` tại mỗi components tuy nhiên nếu có quá nhiều component có cùng một data thay đổi, việc edit ở từng component là không khả thi.
-  Ở Vuex, dùng Getters để định nghĩa `getters`:
   - Ở file `store`
      ```js
      getters: {
        saleProducts: state => {
            var saleProducts = state.products.map( product =>{
              return {
                name: '**' + product.name + '**',
                price: product.price / 2
              }
            })
            return saleProducts
        }
      }
      ```
   - Ở components chỉ cần khai báo trong thuộc tính `computed`:
      ```js
      saleProducts () {
        return this.$store.getters.saleProducts
      }
      ```
4.<strong>`Mutations`</strong>: Thông thường khi xử lý sự kiện, ở các components sẽ định nghĩa event trong `methods`.
- Vuex sử dụng `Mutations` để  thay đổi data và những thay đổi đó được theo dõi(tracking) và có thể kiểm soát qua `Vue dev tools`. Đây là good practice khi xây dựng 1 web Vuex và hỗ trợ tốt khi debugging.
  - Ở file `store`
    ```js
    mutations: {
      reducePrice: state =>  {
          this.$store.state.products.forEach(product =>{
            product.price -= 1
          })
        }
    }
    ```
      >>Bạn có thể truyền thêm tham số cho các handler trong mutation:
      ```js
      mutations: {
        reducePrice: (state, n) =>  {
            this.$store.state.products.forEach(product =>{
              product.price -= n
            })
          }
      }
      ```

  - Ở components, cần commit Mutations trong methods:
      ```js
      reducePrice () {
        this.$store.commit('reducePrice')
      }
      ```
> Khi click button sẽ trigger event reducePrice và sẽ commit một mutation tên là reducePrice

5.<strong>`Actions`</strong>: Actions là hoạt động bất đồng bộ (asynchronous) còn mutations thì là hoạt động đồng bộ (synchronous).
  - Ở components:
  ```js
      this.$store.dispatch('reducePrice')
  ```
>> Nếu truyền thêm tham số vào action và mutation:<br>
  this.$store.dispatch('reducePrice', amount) với amount là tham số cần truyền

  6.<strong>`Mapping Actions & Getters`</strong>: Khi sử dụng nhiều phương thức trong Action và Getters, cách tốt nhất là gọi các phương thức ra thông qua `Map`

- Ở file `maping.vue`
    - import 2 phương thức map
    ```js
    import {mapActions} from 'vuex'
    import {mapGetters} from 'vuex'
    ```
    - Thay vì return ra kết quả `this.$store.getters.saleProducts`, sử dụng map để gọi trực tiếp
    ```js
    ...mapGetters([
      'saleProducts'
    ])
    ```
- Cú pháp map được sử dụng ES6, do đó cần cài đặt babel biên dịch ES6 về javascript để browser có thể hiểu được
```js
  npm install babel-preset-stage-2 --save-dev
```
- Trong file .babelrc

```js
{
  "presets": [
    ["env", { "modules": false }],
    ["stage-2"]
  ]
}
```
