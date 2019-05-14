<style lang="scss" scoped>
.upload-queue{
  .el-upload-list__item:hover{
    .el-upload-list__item-actions{
      display: block;
      opacity: 1;
    }
  }
}
</style>

<template>
<div class="upload-queue" @change="updateList(imgList)">
  <draggable v-model="imgList" @start="drag=true" @end="drag=false" 
    class="el-upload-list el-upload-list--picture-card">
    <div class="el-upload-list__item" v-for="(item, index) in imgList" :key="index">
      <img :src="item" class="el-upload-list__item-thumbnail">

      <span class="el-upload-list__item-actions">
        <span class="el-upload-list__item-preview" @click="handlePreview(item)">
          <i class="el-icon-zoom-in"></i>
        </span>
        <span class="el-upload-list__item-delete" @click="handleRemove(item, index)">
          <i class="el-icon-delete"></i>
        </span>
      </span>
    </div>
  </draggable>
  <el-upload
    v-if="imgList.length < max"
    class="el-upload el-upload--picture-card"
    :action="action"
    :show-file-list="false"
    :on-success="handleSuccess"
    :on-error="handleError"
    :before-upload="beforeUpload">
    <i class="el-icon-plus"></i>
  </el-upload>

  <el-dialog :visible.sync="dialogVisible">
    <img width="100%" :src="dialogImageUrl">
  </el-dialog>
</div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: 'ElUploadSortable',
  components: { draggable },
  props: {
    max: {
      type: Number,
      default: 15
    },
    action: {
      type: String,
      default: 'https://jsonplaceholder.typicode.com/posts/'
    },
    list: {
      type: Array,
      default: () => []
    }
  },

  computed:{
    imgList: {
      get() {
        return this.list
      },
      set(val) {
        this.updateList(val)
      }
    }
  },
  data () {
    return {
      drag: false,
      dragOptions: {
        animation: 200,
        group: "description",
        disabled: false,
        ghostClass: "ghost"
      },
      dialogImageUrl: "",
      dialogVisible: false
    }
  },
 
  methods: {
    updateList(list){
      this.$emit('change', list)
    },
    beforeUpload(file) {
      const isValidFormat = ["image/jpeg", "image/png"].indexOf(file.type) > -1;
      const isLt2M = file.size / 1024 / 1024 < 2; // 2M

      if (!isValidFormat) {
        this.$message.error("图片只能是 JPG或PNG 格式!");
      } else if (!isLt2M) {
        this.$message.error("图片大小不能超过 2MB!");
      }

      const maxLt = this.max === 1 && this.imgList.length > 0;
      if(maxLt){
        this.$message.error("只能上传一张图片，请删除后再上传!");
      }
      return isValidFormat && isLt2M && !maxLt;
    },

    handleSuccess(res, file) {
      this.imgList.push(URL.createObjectURL(file.raw));
      this.$emit('change', this.imgList)
    },

    handleError(err) {
      this.$message.error("上传失败!");
    },

    handleRemove(file, index) {
      this.imgList.splice(index, 1);
      this.$emit('change', this.imgList)
    },

    handlePreview(url) {
      this.dialogImageUrl = url;
      this.dialogVisible = true;
    }
  }
}
</script>
