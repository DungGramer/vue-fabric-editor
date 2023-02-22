<!--
 * @Author: 秦少卫
 * @Date: 2022-09-03 19:16:55
 * @LastEditors: 秦少卫
 * @LastEditTime: 2023-02-08 00:07:57
 * @Description: 插入SVG元素
-->

<template>
<div style="display:inline-block">
  <Dropdown transfer-class-name="fix" @on-click="insertTypeHand">
      <Button size="small">
        {{ $t('insert') }}<Icon type="ios-arrow-down"></Icon>
      </Button>
        <template #list>
            <DropdownMenu >
                <DropdownItem name="insertImg">{{ $t('insert_picture') }}</DropdownItem>
                <DropdownItem name="insert">{{  $t('select_image') }}</DropdownItem>
            </DropdownMenu>
        </template>
    </Dropdown>
    <!-- 插入图片 -->
  <Modal
      v-model="showImgModal"
      :title="$t('please_choose')"
      @on-ok="insertImgFile"
      @on-cancel="showImgModal = false,imgFile = null "
    >
      <Upload :before-upload="handleUploadImg" action="#">
        <Button icon="ios-cloud-upload-outline">{{  $t('insert_picture') }}</Button>
      </Upload>
    </Modal>
  <!--  插入SVG -->
  <Modal
        v-model="showModal"
        :title="$t('please_choose')"
        @on-ok="insertSvg"
        @on-cancel="showModal = false">
        <RadioGroup v-model="insertType" type="button" button-style="solid" style="padding-bottom:  10px">
            <Radio label="string">{{ $t('string') }}</Radio>
            <Radio label="file">{{ $t('file') }}</Radio>
        </RadioGroup>
        <!-- 字符串 -->
        <Input v-if="insertType === 'string'" v-model="svgStr" show-word-limit type="textarea" placeholder="请输入SVG字符" />
        <!-- 文件 -->
        <Upload v-if="insertType === 'file'" :before-upload="handleUpload" action="#">
            <Button icon="ios-cloud-upload-outline">{{ $t('select_svg') }}</Button>
        </Upload>
    </Modal>

</div>
</template>

<script>
import { getImgStr } from '@/utils/utils';
import select from '@/mixins/select';
import { v4 as uuid } from 'uuid';
import { UploadImg } from '@/services/api/index';

export default {
  name: 'ToolBar',
  mixins: [select],
  data() {
    return {
      // 插入图片
      showImgModal: false,
      imgFile: null,
      // 插入SVG
      insertType: 'string', // 插入类型 file | string
      showModal: false,
      svgStr: '',
      svgFile: null,
    };
  },
  methods: {
    insertTypeHand(type) {
      this[type]();
    },
    // 插入图片
    insertImg() {
      this.imgFile = '';
      this.showImgModal = true;
    },
    insertImgFile() {
      const imgEl = document.createElement('img');
      imgEl.src = this.imgFile;
      // 插入页面
      document.body.appendChild(imgEl);
      imgEl.onload = () => {
        // 创建图片对象
        const imgInstance = new this.fabric.Image(imgEl, {
          id: uuid(),
          name: '图片1',
          left: 100,
          top: 100,
        });
        // 设置缩放
        this.canvas.c.add(imgInstance);
        this.canvas.c.setActiveObject(imgInstance);
        this.canvas.c.renderAll();
        // 删除页面中的图片元素
        imgEl.remove();
      };
    },
    handleUploadImg(file) {
      getImgStr(file).then((res) => {
        this.imgFile = res;
      });

      UploadImg(file).then((res) => {
        console.log('📕 res - 111:importFile.vue \n', res);
      });
    },
    // 插入SVG
    insert() {
      this.svgStr = '';
      this.svgFile = null;
      this.showModal = true;
    },
    insertSvg() {
      if (this.insertType === 'string') {
        this.insertSvgStr();
      } else {
        this.insertSvgFile();
      }
    },
    // 插入字符串元素
    insertSvgStr() {
      const This = this;
      this.fabric.loadSVGFromString(this.svgStr, (objects, options) => {
        const item = This.fabric.util.groupSVGElements(objects, { ...options, name: 'defaultSVG', id: uuid() });
        This.canvas.c.add(item).centerObject(item).renderAll();
      });
    },
    // 插入文件元素
    insertSvgFile() {
      const This = this;
      this.fabric.loadSVGFromURL(this.svgFile, (objects, options) => {
        const item = This.fabric.util.groupSVGElements(objects, { ...options, name: 'defaultSVG', id: uuid() });
        This.canvas.c.add(item).centerObject(item).renderAll();
      });
    },
    handleUpload(file) {
      getImgStr(file).then((res) => {
        this.svgFile = res;
      });
    },
  },
};
</script>

<style scoped lang="less">
/deep/ .ivu-select-dropdown{
  z-index: 999;
}
</style>
