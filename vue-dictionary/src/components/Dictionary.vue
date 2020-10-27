<template>
  <div class="container">
    <div id="app">
      <h1>Học từ vựng Tiếng Anh</h1>
      <div class="range-search">
        <!-- Thẻ <b-button> được xây dựng sẵn trong Bootstrap Vue. Tại đây có các thuộc tính sau:
              - size   : size của button
              - variant: màu của button
              - @click : Sự kiện khi click vào thì một modal sẽ hiện ra.
        -->
        <b-button 
          size="md"
          variant="outline-success"
          @click="$bvModal.show('modal-scoped')"
        >
        <!-- Thẻ <b-icon > dùng để gắn icon. Trong đó, cần quan tâm đến thuộc tính sau:
              - icon : hình icon tương ứng, ở đây mình cần một nút + nên icon ="plus". Các bạn xem thêm tại Bootstrap Vue  
        -->
          <b-icon icon="plus" animation="cylon" font-scale="1"> </b-icon>
        </b-button>
        <!-- Đây là phần nội dung khi click vào nút thêm thì Modal hiện ra -->
        <!-- Có các thuộc tính sau cần quan tâm: -->
        <!--    - id: dùng để focus với hàm show của sự kiện click ở trên
                - centered: Xác định vị trí xuất hiện của modal là ở giữa màn hình
                - hide-footer: Ẩn đi phần footer của modal
        -->
        <b-modal id="modal-scoped" centered size="lg" hide-footer>  
          <template #modal-header>
            <!-- Phần header của Modal, mình viết Title cho Modal tại đây -->
            <h5>Thêm từ vựng</h5>
          </template>
          <template #default>
            <template
              >.
              <div role="group">
                <!-- Input dùng để nhập từ Tiếng Anh -->
                <b-form-input id="input-en" v-model="en" :state="checkEn" aria-describedby="input-en-feedback"
                  placeholder="Nhập từ Tiếng Anh"
                  trim
                ></b-form-input>
                <!-- Một đoạn text cảnh báo khi để trống -->
                <b-form-invalid-feedback id="input-en-feedback">
                  Không được bỏ trống
                </b-form-invalid-feedback>
                <!-- Input dùng để nhập từ Tiếng Việt -->
                <b-form-input class="mt-2 mb-3" id="input-vn" v-model="vn" :state="checkVn" aria-describedby="input-vn-help input-vn-feedback" placeholder="Nhập từ Tiếng Việt" trim
                ></b-form-input>
                <!-- Đoạn text cảnh báo khi để trống -->
                <b-form-invalid-feedback id="input-vn-feedback">
                  Không được bỏ trống
                </b-form-invalid-feedback>
              </div>
            </template>
            <div class="btn-modal">
              <!-- Đây là button THÊM từ vựng. Khi đã nhập đủ dữ liệu và click vào nút thì chạy hàm addWord và từ vựng mới sẽ được thêm vào mảng -->
              <b-button variant="success" @click="addWord">Thêm</b-button>
              <!-- Đây là button dùng để đóng Modal -->
              <b-button
                size="md"
                variant="danger"
                @click="$bvModal.hide('modal-scoped')"
                class="ml-3"
              >
                Đóng
              </b-button>
            </div>
          </template>
        </b-modal>
        <!-- Đây là phần nội dung của thanh tìm kiếm -->
        <b-input-group size="md" class="mb-2" :class="styleInput">
          <b-input-group-prepend is-text>
            <!-- Icon search -->
            <b-icon icon="search"></b-icon>
          </b-input-group-prepend>
          <!-- Input text dùng để nhập từ khóa cần tìm, dùng v-model để luôn cập nhật dữ liệu của biến keywords -->
          <b-form-input
            type="search"
            placeholder="Search words"
            v-model="keywords"
          ></b-form-input>
        </b-input-group>
        <!-- Bộ lọc các từ vựng bằng Select -->
        <template>
          <div id="form-select">
            <!--  Sử dụng v-model để lấy giá trị của biến filterStatus
                    -Thuộc tính options: dùng để nạp dữ liệu cho các option của select
             -->
            <b-form-select v-model="filterStatus" :options="options">
            </b-form-select>
          </div>
        </template>
      </div>
      <!-- <div v-for="(item, key) in arrWords" v-bind:key="key">
        <div class="bg-word" v-if="filterWord(item.memorized)">
          <h3 :style="{ color: item.memorized === true ? 'green' : 'red' }">
            {{ item.en }}
          </h3>
          <p>{{ item.vn }}</p>
          <button v-on:click="deleteWord(item.id)">Xóa</button>
          <button v-on:click="item.memorized = !item.memorized">
            {{ item.memorized == true ? "Chưa thuộc" : "Đã thuộc" }}
          </button>
        </div>
      </div> -->
      <template>
        <div>
          <!-- Các thuộc tính cần quan tâm trong thẻ <b-table>
                -items: Mảng dữ liệu sẽ nạp vào table
                -fields: Mảng dữ liệu sẽ nạp tạo thành các trường của table. Ở demo này, các trường đó sẽ         là:ID,English,Vietnamese và Hành động.
                -sticky-header: có hiển thị thanh cuộn bảng hay không. Giá trị nhận vào là true,false
                -outlined: đường viền của table
                -hover: đổi màu khi rê chuột vào từng dòng của table
                -table-variant: màu của table, ở demo mình dùng giá trị là light
                -head-variant: màu của phần header của table.
          -->
          <b-table
            :items="getWord()"
            :fields="fields"
            sticky-header="true"
            responsive="sm"
            :outlined="true"
            :hover="true"
            table-variant="light"
            head-variant="light"
          >
         
            <template #cell(action)="row">
              <b-button
                v-if="row.item.memorized === true"
                size="md"
                variant="outline-danger"
                class="mr-2"
                @click="changeStyleBtn(row.item)"
                >{{
                  row.item.memorized === true ? "Forget" : "Remembered"
                }}</b-button
              >
              <b-button
                v-if="row.item.memorized === false"
                size="md"
                variant="outline-success"
                class="mr-2"
                @click="changeStyleBtn(row.item)"
                >{{
                  row.item.memorized === true ? "Forget" : "Remembered"
                }}</b-button
              >
              <!-- Nút xóa, truyền vào sự kiện click là hàm deleteWord , hàm nhận vào id của dòng đang focus thông qua row.item.id -->
              <b-button
                size="md"
                variant="outline-warning"
                class="mr-2"
                @click="deleteWord(row.item.id)"
                >Delete</b-button
              >
              <!-- As `row.showDetails` is one-way, we call the toggleDetails function on @change -->
            </template>
          </b-table>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      wordUpdate: [],
      wordSearch: [],
      bFlag: false,
      keywords: "",
      styleInput: "phucem",
      fields: [
        {
          key: "id",
          label: "ID",
          sortable: "true",
        },
        {
          key: "en",
          label: "English ",
          sortable: "true",
        },
        {
          key: "vn",
          label: "Vietnamese",
          sortable: "true",
        },
        {
          key: "action",
          label: "Hành động",
        },
      ],
      arrWords: [
        { id: 1, en: "action", vn: "hành động", memorized: true },
        { id: 2, en: "actor", vn: "diễn viên", memorized: false },
        { id: 3, en: "activity", vn: "hoạt động", memorized: true },
        { id: 4, en: "active", vn: "chủ động", memorized: true },
        { id: 5, en: "bath", vn: "tắm", memorized: false },
        { id: 6, en: "bedroom", vn: "phòng ngủ", memorized: true },
      ],
      en: "",
      vn: "", 
      filterStatus: "all",
      options: [
        { value: "all", text: "Xem tất cả" },
        { value: "remember", text: "Xem những từ đã nhớ" },
        { value: "forget", text: "Xem những từ chưa nhớ" },
      ],
      isAddWord: "",
    };
  },
  // watch: {
  //   filterStatus: function() {
  //     this.getWord();
  //   },
  // },
  methods: {
    changeStyleBtn(item) {
      item.memorized = !item.memorized;
    },
    changeColor(color) {
      this.colorSelected = color;
    },
    addWord() {
      if (this.en.length === 0 || this.vn.length === 0) {
        alert("Không được bỏ trống!");
      } else {
        this.arrWords.unshift({
          id: this.arrWords.length + 1,
          en: this.en,
          vn: this.vn,
          memorized: false,
        });
        this.en = "";
        this.vn = "";
        this.isShow = false;
        alert("Thêm thành công!");
      }
    },
    deleteWord(id) {
      // Cách 2:
      // let confirm = confirm("Bạn có muốn xóa không?");
      // if (confirm===true) {
      const index = this.wordUpdate.findIndex((word) => word.id === id);
      this.wordUpdate.splice(index, 1);
      // }
    },
    searchWord() {
      this.bFlag = true;
    },
    getWord() {
      if (this.filterStatus === "all") {
        if (this.keywords.length > 0) {
          return (this.wordSearch = this.wordUpdate.filter((word) => {
            return (
              word.en.search(this.keywords) != -1 ||
              word.vn.search(this.keywords) != -1
            );
          }));
        } else {
          return (this.wordUpdate = this.arrWords);
        }
      } else if (this.filterStatus === "remember") {
        if (this.keywords.length > 0) {
          return (this.wordSearch = this.wordUpdate.filter((word) => {
            return (
              word.en.search(this.keywords) != -1 ||
              word.vn.search(this.keywords) != -1
            );
          }));
        } else {
          return (this.wordUpdate = this.arrWords.filter(
            (word) => word.memorized === true
          ));
        }
      } else if (this.filterStatus === "forget") {
        if (this.keywords.length > 0) {
          return (this.wordSearch = this.wordUpdate.filter((word) => {
            return (
              word.en.search(this.keywords) != -1 ||
              word.vn.search(this.keywords) != -1
            );
          }));
        } else {
          return (this.wordUpdate = this.arrWords.filter(
            (word) => word.memorized === false
          ));
        }
      }
    },
  },
  computed: {
    checkVn() {
      return this.vn.length >= 1;
    },
    checkEn() {
      return this.en.length >= 1;
    },
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
ul {
  padding: 0;
}

.bg-word {
  width: 15%;
  height: 15%;
  padding: 20px;
  background: lightgoldenrodyellow;
  border: 1px solid #000;
  border-radius: 4px;
  margin-top: 20px;
  margin: 30px auto;
}
.bg-word button {
  margin-left: 20px;
}
.chuaNho {
  color: red;
}
.daNho {
  color: green;
}
.range-search {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  width: 100%;
  align-items: end;
}
.phucem {
  width: 47% !important;
}
#form-select {
  width: 47%;
  /* margin-right:  75px; */
}
.btn-modal {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
