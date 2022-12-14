<template>
  <div v-loading="loading" class="product-edit">
    <div>
      <el-form
        label-width="200px"
        class="product-edit__form"
        status-icon
        @keydown.enter="submit"
      >
        <el-form-item class="edit__title">
          <div class="form-title">공연 수정</div>
        </el-form-item>
        <el-form-item label="공연 이름" class="form__composition">
          <el-input
            v-model="title"
            type="text"
            :placeholder="oldDetail.title"
          />
        </el-form-item>
        <el-form-item label="가격" class="form__composition">
          <el-input
            v-model="price"
            type="number"
            :placeholder="oldDetail.price"
          />
        </el-form-item>
        <el-form-item label="상세 설명" class="form__composition">
          <el-input
            v-model="description"
            type="textarea"
            :placeholder="oldDetail.description"
            :autosize="{ minRows: 3, maxRows: 5 }"
          />
        </el-form-item>
        <el-form-item label="검색 태그" class="form__composition">
          <el-select
            v-model="age"
            class="edit__tags"
            :placeholder="oldDetail.tags && oldDetail.tags[0]"
            size="large"
          >
            <el-option
              v-for="item in ageOptions"
              :key="item"
              :label="item"
              :value="item"
            />
          </el-select>
          <el-select
            v-model="genre"
            class="edit__tags"
            :placeholder="oldDetail.tags && oldDetail.tags[1]"
            size="large"
          >
            <el-option
              v-for="item in genreOptions"
              :key="item"
              :label="item"
              :value="item"
            />
          </el-select>
          <el-select
            v-model="openrun"
            class="edit__tags"
            placeholder="오픈런"
            size="large"
          >
            <el-option
              v-for="item in openrunOptions"
              :key="item"
              :label="item"
              :value="item"
            />
          </el-select>
          <el-select
            v-model="region"
            class="edit__tags"
            :placeholder="oldDetail.tags && oldDetail.tags[3]"
            size="large"
          >
            <el-option
              v-for="item in regionOptions"
              :key="item"
              :label="item"
              :value="item"
            />
          </el-select>
        </el-form-item>
        <div class="img-container">
          <el-form-item
            id="thumbnailFileList"
            label="썸네일 이미지"
            class="form__composition"
          >
            <el-upload
              ref="thumbnailFileList"
              :file-list="thumbnailFileList"
              action="#"
              :auto-upload="false"
              :on-change="thumbnailHandleAvatarSuccess"
              :limit="1"
              list-type="picture-card"
            >
              <el-icon>
                <Plus />
              </el-icon>
              <template #file="{ file }">
                <div>
                  <img
                    class="el-upload-list__item-thumbnail"
                    :src="file.url"
                    alt=""
                  />
                  <span class="el-upload-list__item-actions">
                    <span
                      class="el-upload-list__item-delete"
                      @click="handleAvatarRemove"
                    >
                      <el-icon><Delete /></el-icon>
                    </span>
                  </span>
                </div>
              </template>
            </el-upload>
          </el-form-item>
          <el-form-item
            id="detailFileList"
            label="상세 이미지"
            class="form__composition"
          >
            <el-upload
              ref="detailFileList"
              :file-list="detailFileList"
              action="#"
              :auto-upload="false"
              :on-change="detailHandleAvatarSuccess"
              :limit="1"
              list-type="picture-card"
            >
              <el-icon>
                <Plus />
              </el-icon>
              <template #file="{ file }">
                <div>
                  <img
                    class="el-upload-list__item-detail"
                    :src="file.url"
                    alt=""
                  />
                  <span class="el-upload-list__item-actions">
                    <span
                      class="el-upload-list__item-delete"
                      @click="handleAvatarRemove"
                    >
                      <el-icon><Delete /></el-icon>
                    </span>
                  </span>
                </div>
              </template>
            </el-upload>
          </el-form-item>
        </div>
        <el-form-item>
          <el-switch
            v-model="isSoldOut"
            size="large"
            class="is-soldout"
            active-text="매진"
            inactive-text="판매"
          />
        </el-form-item>
        <el-form-item>
          <el-button
            class="product-edit__btn"
            type="primary"
            :disabled="isDone"
            @click="submit"
          >
            {{ isDone ? '편집됨' : '편집하기' }}
          </el-button>
          <el-button class="product-edit__btn" type="danger" @click="deletePf">
            {{ isDeleted ? '삭제됨' : '삭제하기' }}
          </el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import { Plus, Delete } from '@element-plus/icons-vue';

  export default {
    components: {
      Plus,
      Delete,
    },
    data() {
      return {
        ageOptions: [
          '전체 관람가',
          '만 7세 이상',
          '만 15세 이상',
          '만 19세 이상',
        ],
        genreOptions: [
          '연극',
          '뮤지컬',
          '무용',
          '클래식',
          '오페라',
          '국악',
          '복합',
        ],
        openrunOptions: ['Y', 'N'],
        regionOptions: [
          '서울',
          '인천',
          '대전',
          '대구',
          '울산',
          '부산',
          '광주',
          '세종',
          '경기도',
          '강원',
          '충남',
          '충북',
          '경북',
          '경남',
          '전북',
          '전남',
          '제주',
        ],
        title: '',
        price: '',
        description: '',
        region: '',
        age: '',
        genre: '',
        openrun: '',
        thumbnailBase64: '',
        detailBase64: '',
        isSoldOut: false,
        isDone: false,
        isDeleted: false,
      };
    },
    computed: {
      oldDetail() {
        return this.$store.state.admin.willEdit;
      },
      data() {
        return {
          ageOptions: [
            '전체 관람가',
            '만 7세 이상',
            '만 15세 이상',
            '만 19세 이상',
          ],
          genreOptions: [
            '연극',
            '뮤지컬',
            '무용',
            '클래식',
            '오페라',
            '국악',
            '복합',
          ],
          openrunOptions: ['Y', 'N'],
          regionOptions: [
            '서울',
            '인천',
            '대전',
            '대구',
            '울산',
            '부산',
            '광주',
            '세종',
            '경기도',
            '강원',
            '충남',
            '충북',
            '경북',
            '경남',
            '전북',
            '전남',
            '제주',
          ],
          title: '',
          price: '',
          description: '',
          region: '',
          age: '',
          genre: '',
          openrun: '',
          thumbnailBase64: '',
          photoBase64: '',
          isSoldOut: false,
          isDone: false,
          isDeleted: false,
        };
      },
    },
    computed: {
      oldDetail() {
        return this.$store.state.admin.willEdit;
      },
      loading() {
        return this.$store.state.admin.loading;
      },
    },
    created() {
      this.$store.dispatch('admin/showDetail', this.$route.params.adminId);
    },
    methods: {
      handleAvatarRemove(uploadFile) {
        const file = uploadFile.replace('FileList', 'Base64');
        this[file] = '';
        const uploader = this.$refs[uploadFile].$el;
        const inputCard = uploader.querySelector('.el-upload--picture-card');
        inputCard.style.display = 'inline-flex';
      },
      async thumbnailHandleAvatarSuccess(uploadFile) {
        const uploader = this.$refs.thumbnailFileList.$el;
        const inputCard = uploader.querySelector('.el-upload--picture-card');
        inputCard.style.display = 'none';
        this.thumbnailBase64 = await this.toBase64(uploadFile.raw);
      },
      async detailHandleAvatarSuccess(uploadFile) {
        const uploader = this.$refs.detailFileList.$el;
        const inputCard = uploader.querySelector('.el-upload--picture-card');
        inputCard.style.display = 'none';
        this.detailBase64 = await this.toBase64(uploadFile.raw);
      },
      async submit() {
        try {
          console.log('submit edit');
          await this.$store.dispatch('admin/editProduct', {
            productId: this.$route.params.adminId,
            data: {
              title: this.title || null,
              price: this.price || null,
              description: this.description || null,
              tags: [
                this.age || this.oldDetail.tags[0],
                this.genre || this.oldDetail.tags[1],
                this.openrun || this.oldDetail.tags[2],
                this.region || this.oldDetail.tags[3],
              ],
              thumbnailBase64: this.thumbnailBase64 || null,
              photoBase64: this.detailBase64 || null,
              isSoldOut: this.isSoldOut,
            },
          });
          this.isDone = true;
          this.$router.push('/admin/allsales');
        } catch (error) {
          console.log(error);
          alert('공연 편집이 되지 않았습니다.');
        }
      },
      async deletePf() {
        const res = confirm(
          '이 공연을 삭제하시겠습니까? 복구는 불가능 합니다.\n일시적인 것 이라면 "매진"처리를 하세요.',
        );
        if (!res) return;
        await this.$store.dispatch(
          'admin/DeleteProduct',
          this.$route.params.adminId,
        );
        this.isDeleted = true;
        alert('삭제되었습니다.');
        this.$router.push('/admin/allsales');
      },
    },
  };
</script>

<style lang="scss" scoped>
  .product-edit {
    display: flex;
    align-items: center;
    margin: auto;
    width: 1000px;
    height: max(700px, 70vh);
    position: relative;
    display: flex;
    background-color: #2b2b2b;
    color: #eee;
    border-radius: 10px;
    --el-text-color-regular: #e5eaf3;
    --el-text-color-primary: #eee;
    --el-input-bg-color: #2b2b2b;
    --el-fill-color-blank: #2b2b2b;
    --el-border-color: #4c4d4f;
    .edit__title {
      margin-bottom: 40px;
      .form-title {
        font-size: 23px;
        font-weight: 700;
      }
    }
    .edit__tags {
      width: 150px;
      margin-right: 15px;
      &:last-child {
        margin-right: 0;
      }
    }
    .img-container {
      display: flex;
    }
    .is-soldout {
      --el-switch-on-color: #ff4949;
      --el-switch-off-color: #79bbff;
    }
    .product-edit__btn {
      padding: 20px 15px;
    }
  }
</style>
