<template>
  <div>
    <el-dialog
      :title="info.isAdd ? '添加轮播图' : '编辑轮播图'"
      :visible.sync="info.isshow"
      @closed="close"
    >
      <el-form ref="form" :model="form" label-width="80px" :rules="rules">
        <el-form-item label="标题" prop="title">
          <el-input v-model="form.title"></el-input>
        </el-form-item>

        <!-- 上传一张图片原生写法 -->
        <el-form-item label="图片" prop="img">
          <div class="my-upload">
            <h2>+</h2>
            <img class="img" :src="imgUrl" alt="" v-if="imgUrl" />
            <input
              v-if="info.isshow"
              type="file"
              @change="getFile"
              class="my-input"
            />
          </div>
        </el-form-item>

        <!-- 上传多张el框架 -->
        <!-- <el-form-item label="图片">
          <el-upload action="#" list-type="picture-card" :auto-upload="false">
            <i slot="default" class="el-icon-plus"></i>
            <div slot="file" slot-scope="{ file }">
              <img
                class="el-upload-list__item-thumbnail"
                :src="file.url"
                alt=""
              />
              <span class="el-upload-list__item-actions">
                <span
                  class="el-upload-list__item-preview"
                  @click="handlePictureCardPreview(file)"
                >
                  <i class="el-icon-zoom-in"></i>
                </span>
                <span
                  v-if="!disabled"
                  class="el-upload-list__item-delete"
                  @click="handleDownload(file)"
                >
                  <i class="el-icon-download"></i>
                </span>
                <span
                  v-if="!disabled"
                  class="el-upload-list__item-delete"
                  @click="handleRemove(file)"
                >
                  <i class="el-icon-delete"></i>
                </span>
              </span>
            </div>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="dialogImageUrl" alt="" />
          </el-dialog>
        </el-form-item> -->

        <el-form-item label="状态">
          <el-switch
            v-model="form.status"
            :active-value="1"
            :inactive-value="2"
          ></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="cancel">取 消</el-button>
        <el-button type="primary" @click="add('form')" v-if="info.isAdd"
          >添 加</el-button
        >
        <el-button type="primary" @click="update('form')" v-else
          >修 改</el-button
        >
      </div>
    </el-dialog>
  </div>
</template>
<script>
import { mapGetters, mapActions } from "vuex";
import { indexRoutes } from "../../../router/index";
import {
  reqBannerAdd,
  reqBannerInfo,
  reqBannerEdit,
} from "../../../utils/request";
import { warningAlert, successAlert } from "../../../utils/alert";
import { validateNecessary } from "../../../utils/validate";
export default {
  props: ["info"],
  components: {},
  data() {
    return {
      imgUrl: "",
      form: {
        title: "",
        img: null,
        status: 1,
      },
      rules: {
        title: [validateNecessary("请输入名称")],
        img: [validateNecessary("请选择图片")],
      },
      dialogImageUrl: "",
      dialogVisible: false,
      disabled: false,
    };
  },
  computed: {
    ...mapGetters({
      list: "banner/list",
    }),
  },
  methods: {
    handleRemove(file) {
      console.log(file);
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    handleDownload(file) {
      console.log(file);
    },
    ...mapActions({
      reqListAction: "banner/reqListAction",
    }),
    //取消
    cancel() {
      this.info.isshow = false;
    },
    close() {
      //如果是添加开的弹框，就什么都不做；如果是编辑开的弹框，就清除form
      if (!this.info.isAdd) {
        this.empty();
      }
    },
    getFile(e) {
      let file = e.target.files[0];
      // console.log(file);
      //   1.大小不超过2M
      if (file.size > 2 * 1024 * 1024) {
        warningAlert("文件的大小不能超过2M");
        return;
      }
      // 2.验证格式
      let imgExtArr = [".jpg", ".png", ".jpeg", ".gif"];
      let extname = file.name.slice(file.name.lastIndexOf("."));
      if (!imgExtArr.some((item) => item == extname)) {
        warningAlert("格式不正确");
        return;
      }
      this.imgUrl = URL.createObjectURL(file);
      this.form.img = file;
    },
    look(id) {
      reqBannerInfo(id).then((res) => {
        if (res.data.code == 200) {
          this.form = res.data.list;
          this.form.id = id;
          this.imgUrl = this.$imgPre + this.form.img;
        } else {
          warningAlert(res.data.msg);
        }
      });
    },
    //修改了pid
    changePid() {
      if (this.form.pid == 0) {
        this.form.type = 1;
      } else {
        this.form.type = 2;
      }
    },
    //数据重置
    empty() {
      this.imgUrl = "";
      this.form = {
        title: "",
        img: null,
        status: 1,
      };
    },
    add(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          reqBannerAdd(this.form).then((res) => {
            if (res.data.code == 200) {
              successAlert(res.data.msg);
              this.empty();
              this.cancel();
              this.reqListAction();
            } else {
              warningAlert(res.data.msg);
            }
          });
        }
      });
    },
    update(form) {
      this.$refs[form].validate((valid) => {
        if (valid) {
          reqBannerEdit(this.form).then((res) => {
            if (res.data.code == 200) {
              successAlert(res.data.msg);
              this.empty();
              this.cancel();
              this.reqListAction();
            } else {
              warningAlert(res.data.msg);
            }
          });
        }
      });
    },
  },
  mounted() {},
};
</script>
<style scoped>
.my-upload {
  width: 150px;
  height: 150px;
  border: 1px dashed #ccc;
  position: relative;
}
.my-upload h2 {
  text-align: center;
  line-height: 150px;
  font-weight: 400;
}
.my-upload .my-input {
  position: absolute;
  width: 150px;
  height: 150px;
  left: 0;
  top: 0;
  opacity: 0;
  cursor: pointer;
}
.img {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
</style>