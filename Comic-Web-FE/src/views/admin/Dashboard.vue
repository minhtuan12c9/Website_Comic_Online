<template>
  <Header />
  <SideBar />
  <div class="mh-100">
    <main id="main" class="main">
      <div class="truyenmoicapnhat">
        <div class="pagetitle">
          <h1 class="fw-bold">QUẢN LÝ TRUYỆN</h1>
        </div>
      </div>
      <div class="d-flex mt-3">
        <div class="">
          <button class="btn btn-success" data-bs-toggle="modal" data-bs-target="#modalThem">Thêm truyện</button>
        </div>
        <div class="w-75">
          <div class="container">
            <ul class="responsive-table">
              <li class="table-header">
                <div class="col col-3 fw-bold text-center">Ảnh</div>
                <div class="col col-2 fw-bold">Tên truyện</div>
                <div class="col col-3 fw-bold text-center me-3">Chương</div>
                <div class="col col-1 fw-bold text-center me-5">Xem</div>
                <div class="col col-1 fw-bold text-center me-5">Sửa</div>
                <div class="col col-1 fw-bold text-center me-5">Xóa</div>
              </li>
              <div style="max-height: 650px" class="overflow-auto">
                <li class="table-row" v-for="story in stories" :key="story.id">
                  <div class="col col-3 text-center">
                    <img :src="`${this.apiUrl}/${story.avt}`" alt="Story Avatar" style="max-width: 100px; max-height: 50px" />
                  </div>
                  <div class="col col-2 h5">{{ story.ten }}</div>
                  <div class="col col-3 text-center" data-label="Customer Name">
                    <RouterLink :to="{ path: `/admin/chi-tiet/${story.id}` }" class="image-link">
                      <button class="btn" style="background-color: #1e3a63; color: white">
                        <i class="bi bi-card-list"></i>
                      </button>
                    </RouterLink>
                  </div>
                  <div class="col col-1 text-center me-3" data-label="Customer Name">
                    <button class="btn" style="background-color: #1e3a63; color: white" data-bs-toggle="modal" data-bs-target="#modalInfo" @click="takeStory(story)">
                      <i class="bi bi-info-circle"></i>
                    </button>
                  </div>
                  <div class="col col-1 text-center me-3" data-label="Customer Name">
                    <button class="btn" style="background-color: #1e3a63; color: white" data-bs-toggle="modal" data-bs-target="#modalUpdate" @click="updateStory(story)">
                      <i class="bi bi-pencil-square"></i>
                    </button>
                  </div>
                  <div class="col col-1 text-center me-3" data-label="Amount">
                    <button class="btn" style="background-color: #1e3a63; color: white" @click="deleteStory(story.id)">
                      <i class="bi bi-trash3"></i>
                    </button>
                  </div>
                </li>
              </div>
            </ul>
          </div>
        </div>
      </div>
    </main>
    <!-- End #main -->
  </div>
  <!-- End #main -->

  <!-- Modal thêm -->
  <div class="modal fade" id="modalThem" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header justify-content-center">
          <h5 class="modal-title fw-bold" id="staticBackdropLabel">Thêm truyện mới</h5>
        </div>
        <div class="modal-body">
          <div class="fw-bold">Ảnh đại diện truyện <span class="text-danger">*</span></div>
          <input type="file" class="form-control" @change="uploadFileStory" />
          <div class="mt-2 fw-bold">Tên truyện <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="newStory.ten" />
          <div class="mt-2 fw-bold">Tác giả <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="newStory.tacgia" />
          <div class="mt-2 fw-bold">Thể loại <span class="text-danger">*</span></div>
          <div class="form-check form-check-inline" v-for="category in categories" :key="category.id">
            <input class="form-check-input" type="checkbox" @change="addSelectedCategoryIds(category.id)" />
            <label class="form-check-label">{{ category.ten }}</label>
          </div>
          <div class="mt-2 fw-bold">Mô tả <span class="text-danger">*</span></div>
          <textarea class="form-control" v-model="newStory.gioithieu"></textarea>
          <div>------------------------------------------------------------------</div>
          <div class="mt-2 fw-bold text-center">Chương đầu tiên <span class="text-danger">*</span></div>
          <div class="mt-2 fw-bold">Số chương <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="newChapter.so" />
          <div class="mt-2 fw-bold">Tên chương <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="newChapter.ten" />
          <div class="mt-2 fw-bold">Nội dung chương <span class="text-danger">*</span></div>
          <input type="file" class="form-control" @change="uploadFileChapter" />
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Hủy</button>
          <button type="button" class="btn btn-primary" id="liveToastBtn" data-bs-dismiss="modal" @click="addStory()">Thêm</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal Thông tin -->
  <div class="modal fade" id="modalInfo" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header justify-content-center">
          <h5 class="modal-title fw-bold" id="staticBackdropLabel">Thông tin truyện</h5>
        </div>
        <div class="modal-body">
          <div class="fw-bold">Ảnh truyện</div>
          <div class="text-center mb-3">
            <img v-if="detailStory && detailStory.avt" :src="`${this.apiUrl}/${detailStory.avt}`" alt="Story Avatar" style="max-width: 400px; max-height: 150px" class="mx-auto" />
          </div>

          <div class="d-flex mt-3">
            <div class="col-4 fw-bold">Tên truyện:</div>
            <div class="col-8">{{ detailStory ? detailStory.ten : "" }}</div>
          </div>

          <div class="d-flex mt-3">
            <div class="col-4 fw-bold">Tác giả:</div>
            <div class="col-8">{{ detailStory ? detailStory.tacgia : "" }}</div>
          </div>

          <div class="d-flex mt-3">
            <div class="col-4 fw-bold">Thể loại:</div>
            <div class="col-8">
              {{ detailStory && detailStory.categoriesForStory ? detailStory.categoriesForStory.join(", ") : "" }}
            </div>
          </div>

          <div class="d-flex mt-3">
            <div class="col-4 fw-bold">Mô tả:</div>
            <div class="col-8">{{ detailStory ? detailStory.gioithieu : "" }}</div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Đóng</button>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal cập nhật -->
  <div class="modal fade" id="modalUpdate" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header justify-content-center">
          <h5 class="modal-title fw-bold" id="staticBackdropLabel">Cập nhật truyện</h5>
        </div>
        <div class="modal-body">
          <div class="fw-bold">Ảnh đại diện truyện <span class="text-danger">*</span></div>
          <input type="file" class="form-control" @change="uploadFileStory" />
          <div class="mt-2 fw-bold">Tên truyện <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="updStory.ten" />
          <div class="mt-2 fw-bold">Tác giả <span class="text-danger">*</span></div>
          <input type="text" class="form-control" v-model="updStory.tacgia" />
          <div class="mt-2 fw-bold">Thể loại <span class="text-danger">*</span></div>
          <div class="form-check form-check-inline" v-for="category in categories" :key="category.id">
            <input class="form-check-input" type="checkbox" @change="updateSelectedCategoryIds(category.id)" />
            <label class="form-check-label">{{ category.ten }}</label>
          </div>
          <div class="mt-2 fw-bold">Mô tả <span class="text-danger">*</span></div>
          <textarea class="form-control" v-model="updStory.gioithieu"></textarea>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Hủy</button>
          <button type="button" class="btn btn-primary" id="liveToastBtn" @click="confirmUpdate()" data-bs-dismiss="modal">Chấp nhận</button>
        </div>
      </div>
    </div>
  </div>

  <!-- TOAST -->
  <button type="button" class="btn btn-primary" id="liveToastBtn">Show live toast</button>

  <div class="position-fixed top-0 end-0 p-3" style="z-index: 11">
    <div id="liveToast" class="toast hide" role="alert" aria-live="assertive" aria-atomic="true">
      <div class="toast-header">
        <strong class="me-auto">Thông báo</strong>
        <small>Ngay bây giờ</small>
        <button type="button" class="btn-close" data-bs-dismiss="toast" aria-label="Close"></button>
      </div>
      <div class="toast-body">Thêm thành công</div>
    </div>
  </div>
</template>

<script>
import SideBar from "./SidebarAdmin.vue";
import Header from "../../components/Header.vue";
import axios from "axios";
import swal from "sweetalert2";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";
import { RouterLink } from "vue-router";

export default {
  name: "HomePage",
  data() {
    return {
      apiUrl: process.env.VUE_APP_URL,
      stories: [],
      categories: [],
      categoriesForStory: [],
      cateOptions: [],
      newStory: {
        avt: null,
        ten: null,
        tacgia: null,
        idTheLoais: [],
        gioithieu: null,
      },
      newChapter: {
        so: null,
        ten: null,
        noidung: null,
      },
      detailStory: null,
      selected: null,
      updStory: {
        id: null,
        avt: null,
        ten: null,
        tacgia: null,
        idTheLoais: [],
        gioithieu: null,
      },
      avtup: null,
      ndChapter: null,

      storyId: null,
    };
  },
  components: { SideBar, Header, RouterLink },
  mounted() {
    this.ShowStories();
    this.ShowCate();
  },
  methods: {
    uploadFileStory(e) {
      this.avtup = e.target.files[0];
    },
    uploadFileChapter(e) {
      this.ndChapter = e.target.files[0];
    },
    addSelectedCategoryIds(id) {
      const idIndex = this.newStory.idTheLoais.findIndex((categoryId) => categoryId === id);
      if (idIndex === -1) {
        this.newStory.idTheLoais.push(id);
      } else {
        this.newStory.idTheLoais.splice(idIndex, 1);
      }
      console.log(this.newStory.idTheLoais);
    },
    updateSelectedCategoryIds(id) {
      const idIndex = this.newStory.idTheLoais.findIndex((categoryId) => categoryId === id);
      if (idIndex === -1) {
        this.updStory.idTheLoais.push(id);
      } else {
        this.updStory.idTheLoais.splice(idIndex, 1);
      }
      console.log(this.updStory.idTheLoais);
    },
    async ShowStories() {
      try {
        const reponse = await axios.get("http://localhost:8000/api/story");
        this.stories = reponse.data;
        this.stories.reverse();
        this.storyId = this.stories.id;
        console.log("DANH SÁCH TRUYỆN:: ", this.stories);
      } catch (error) {
        console.error("Error fetching stories data:", error);
      }
    },
    async fetchStoryCategories() {
      for (let i = 0; i < this.stories.length; i++) {
        const storyId = this.stories[i].id;
        try {
          // Gọi API để lấy danh sách thể loại cho mỗi truyện
          const response = await axios.get(`http://localhost:8000/api/story/${storyId}/categories`);
          this.stories[i].storyCategories = response.data;
        } catch (error) {
          console.error(`Error fetching categories for story with ID ${storyId}:`, error);
        }
      }
    },
    async ShowCate() {
      try {
        const reponse = await axios.get("http://localhost:8000/api/category");
        this.categories = reponse.data;
      } catch (error) {
        console.error("Error fetching categories data:", error);
      }
    },
    async addStory() {
      try {
        const formDataStory = new FormData();
        formDataStory.append("avtFile", this.avtup);
        formDataStory.append("ten", this.newStory.ten);
        formDataStory.append("tacgia", this.newStory.tacgia);
        formDataStory.append("gioithieu", this.newStory.gioithieu);
        formDataStory.append("idTheLoais", this.newStory.idTheLoais);

        const formDataChapter = new FormData();
        formDataChapter.append("so", this.newChapter.so);
        formDataChapter.append("ten", this.newChapter.ten);
        formDataChapter.append("noidung", this.ndChapter);

        console.log("Dữ liệu form data story:: ", formDataStory);
        await axios.post("http://localhost:8000/api/story/add", formDataStory, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        });

        const response = await axios.get("http://localhost:8000/api/story");
        const newStory = response.data[response.data.length - 1];
        this.storyId = newStory.id;
        console.log("ID STORY ĐƯỢC THÊM:: ", this.storyId);

        console.log("Dữ liệu form data chapter:: ", formDataChapter);
        await axios.post(this.apiUrl + `/api/chapter/${this.storyId}/add`, formDataChapter, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        });
        this.ShowStories();
        console.log("Post add story", response.data);
        swal
          .fire({
            title: "Đã thêm",
            text: "Đã thêm truyện mới thành công",
            icon: "success",
          })
          .then(() => {
            window.location.reload();
          });
        this.newStory = {
          avt: null,
          ten: null,
          tacgia: null,
          gioithieu: null,
        };
        this.newChapter = {
          so: null,
          ten: null,
          noidung: null,
        };
        this.selected = null;
      } catch (error) {
        console.error("Error adding story:", error);
      }
    },
    async takeStory(story) {
      this.detailStory = story;
      console.log(this.detailStory);
      try {
        const response = await axios.get(this.apiUrl + `/api/story/${story.id}/categories`);
        const categories = response.data;
        const categoryNames = categories.map((category) => category.ten);
        this.detailStory.categoriesForStory = categoryNames;
        console.log("Truyện có id là ", this.detailStory.id + " có thể loại: " + categoryNames);
      } catch (error) {
        console.error("Error taking categories for story:", error);
      }
    },
    async updateStory(story) {
      this.updStory = story;
      this.updStory.idTheLoais = [];
    },
    async confirmUpdate() {
      try {
        const formData = new FormData();
        formData.append("avtFile", this.avtup);
        formData.append("ten", this.updStory.ten);
        formData.append("tacgia", this.updStory.tacgia);
        formData.append("gioithieu", this.updStory.gioithieu);
        formData.append("idTheLoais", this.updStory.idTheLoais);

        await axios.put(`http://localhost:8000/api/story/update/${this.updStory.id}`, formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        });
        this.ShowStories();
        swal.fire({
          title: "Đã cập nhật",
          text: "Đã cập nhật truyện thành công!",
          icon: "success",
        });
        this.updStory = {
          id: null,
          avt: null,
          ten: null,
          tacgia: null,
          idTheLoais: [],
          gioithieu: null,
        };
      } catch (error) {
        console.error("Error taking update story:", error);
      }
    },
    deleteStory(strId) {
      this.selected = this.stories.find((story) => story.id === strId);
      const deleteStoryId = this.selected.id;
      swal
        .fire({
          title: "Bạn muốn xóa truyện?",
          text: "Xóa truyện sẽ xóa tất cả chương, bình luận, lịch sử, yêu thích của người dùng. Bạn có chắc muốn xóa không?",
          icon: "warning",
          showCancelButton: true,
          confirmButtonColor: "#3085d6",
          confirmButtonText: "Có, hãy xóa",
          cancelButtonColor: "#d33",
        })
        .then((result) => {
          if (result.isConfirmed) {
            axios
              .delete(this.apiUrl + `/api/story/remove/${deleteStoryId}`)
              .then((response) => {
                swal.fire({
                  title: "Đã xóa",
                  text: "Bạn đã xóa truyện thành công",
                  icon: "success",
                });
                this.ShowStories();
              })
              .catch((error) => {
                swal.fire({
                  title: "Lỗi",
                  text: "Có lỗi xảy ra khi xóa truyện",
                  icon: "error",
                });
                console.error(error);
              });
          }
        });
    },
  },
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

body {
  font-family: "lato", sans-serif;
}

.container {
  border-radius: 15px;
  max-width: 1000px;
  margin-left: auto;
  margin-right: auto;
  padding-left: 10px;
  padding-right: 10px;
  font-family: "Nunito", sans-serif;
}

h2 {
  font-size: 26px;
  margin: 20px 0;
  text-align: center;

  small {
    font-size: 0.5em;
  }
}

.responsive-table {
  li {
    border-radius: 3px;
    padding: 15px 30px;
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }

  .table-header {
    border-radius: 10px;
    color: #ffffff;
    background-color: #1e3a63;
    font-size: 16px;
    text-transform: uppercase;
    letter-spacing: 0.03em;
  }

  .table-row {
    border-radius: 10px;
    align-items: center;
    background-color: #ffffff;
    box-shadow: 0px 0px 9px 0px rgba(0, 0, 0, 0.1);
  }

  .col-1 {
    flex-basis: 5%;
  }

  .col-2 {
    flex-basis: 35%;
  }

  .col-3 {
    flex-basis: 15%;
  }

  .col-4 {
    flex-basis: 15%;
  }

  .col-5 {
    flex-basis: 70%;
  }

  @media all and (max-width: 767px) {
    .table-header {
      display: none;
      color: #ffffff;
    }

    .table-row {
    }

    li {
      display: block;
    }

    .col-2 {
      flex-basis: 35%;
    }
  }
}
</style>

<style>
.carousel-small,
.carousel-small .carousel-inner,
.carousel-small img {
  height: 500px !important;
}

.carousel-small .carousel-control-prev-icon::after,
.carousel-small .carousel-control-next-icon::after {
  content: "";
}
</style>
