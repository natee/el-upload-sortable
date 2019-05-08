<template>
  <div>
    <el-upload-sortable :max="3" @change="picUpdate(list, 'xxx')"></el-upload-sortable>
    <p>只能上传2M以内jpg或png图片，可以拖拽图片调整顺序，最多上传3张</p>
  </div>
</template>

<script>
import ElUploadSortable from 'el-upload-sortable'
export default {
  name: 'demo',
  components: {
    ElUploadSortable
  },
  methods: {
    picUpdate(list, arg1){
      console.log(list);
    }
  }
}
</script>
