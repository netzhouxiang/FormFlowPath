<template>
  <div class="designer">
    <div class="header">
      <div class="tabD">
        <div class="tabs">
          <div class="tab">
            <span class="roundNumber">1</span><span>基础设置</span>
          </div>
          <div class="tab active">
            <span class="roundNumber">2</span><span>表单设计</span>
          </div>
          <div class="tab">
            <span class="roundNumber">3</span><span>流程设计</span>
          </div>
          <div class="tab">
            <span class="roundNumber">4</span><span>高级设置</span>
          </div>
        </div>
      </div>
    </div>
    <div class="content">
      <div class="left">
        <div class="title">基础组件</div>
        <draggable class="items" :list="dragList" :force-fallback="true" @start="onStart" @end="onEnd" :clone="cloneDefaultField" :group="{ name: 'list', pull: 'clone',put:false }" :sort="false" itemKey="id">
          <template #item="{ element }">
            <div class="item">
              <div class="unit">
                <i class="iconfont" :class="element.icon"></i>
                <span>{{ element.props.label }}</span>
              </div>
            </div>
          </template>
        </draggable>
      </div>
      <div class="panl">
        <el-form readonly label-width="120px">
          <draggable :list="widgetList" id="panl" class="drag-content" @start="onStart2" @end="onEnd2" ghost-class="ghost" itemKey="id" :force-fallback="true" group="list" :fallback-class="true" :fallback-on-body="true">
            <template #item="{ element, index }">
              <el-alert class="itemx" :active="element.active" @click="changeActive(element, index)" v-if="element.componentName === 'ShuoMingField'" :title="element.props.defaultValue" :closable="false" type="warning" />
              <el-form-item v-else :active="element.active" @click="changeActive(element, index)" :required="element.props.required" class="item" :label="element.props.label">
                <el-input v-if="element.componentName === 'TextField'" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-input v-if="element.componentName === 'TextAreaField'" type="textarea" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-input-number v-if="element.componentName === 'NumberField'" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-radio-group v-if="element.componentName === 'RadioField'" v-model="element.props.defaultValue">
                  <el-radio v-for="(item,index) in element.props.options" :key="index" :label="item.value">{{item.label}}</el-radio>
                </el-radio-group>
                <el-checkbox-group v-if="element.componentName === 'CheckboxField'" v-model="element.props.defaultValue">
                  <el-checkbox v-for="(item,index) in element.props.options" :key="index" :label="item.value">{{item.label}}</el-checkbox>
                </el-checkbox-group>
                <el-date-picker v-if="element.componentName === 'DatePickerField'" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-date-picker v-if="element.componentName === 'DateRangePickerField'" v-model="element.props.defaultValue" type="daterange" range-separator="-" :start-placeholder="element.props.startPlaceholder" :end-placeholder="element.props.endPlaceholder" />
                <el-input v-if="element.componentName === 'TextCardField'" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-input v-if="element.componentName === 'TextPhoneField'" v-model="element.props.defaultValue" :placeholder="element.props.placeholder" />
                <el-upload v-if="element.componentName === 'UploadFileField'" v-model:file-list="element.props.defaultValue" class="upload-demo" action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15" multiple>
                  <el-button type="primary" plain>
                    <el-icon style="vertical-align: middle">
                      <Search />
                    </el-icon>
                    添加
                  </el-button>
                </el-upload>
                <el-upload v-if="element.componentName === 'UploadImageField'" v-model:file-list="element.props.defaultValue" action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15" list-type="picture-card">
                  <el-icon>
                    <Plus />
                  </el-icon>
                </el-upload>
                <!-- <div class="btn">
                  <el-icon>
                    <CopyDocument />
                  </el-icon>
                  <el-icon>
                    <Delete />
                  </el-icon>
                </div> -->
              </el-form-item>

            </template>
          </draggable>
        </el-form>
        <div class="drag">
          <img src="../assets/drag.png" alt="">
          点击或拖拽左侧控件至此处
        </div>
      </div>
      <div class="right" v-if="widgetList.length>0">
        <div class="title">
          <i class="iconfont" :class="widgetList[IndexActive].icon"></i>
          <span>{{widgetList[IndexActive].name}}</span>
        </div>
        <div class="list">
          <div class="item">
            <div class="label">
              <span>标题</span>
              <span>最多50字</span>
            </div>
            <el-input maxlength="50" v-model="widgetList[IndexActive].props.label" placeholder="" />
          </div>
          <div class="item">
            <div class="label">
              <span>提示文字</span>
              <span>最多50字</span>
            </div>
            <el-input maxlength="50" v-model="widgetList[IndexActive].props.placeholder" placeholder="" />
          </div>
          <div class="item">
            <div class="label">
              <span>默认值</span>
              <span></span>
            </div>
            <el-input maxlength="50" v-model="widgetList[IndexActive].props.defaultValue" placeholder="" />
          </div>
          <div class="item">
            <div class="label">
              <span>必填</span>
              <span></span>
            </div>
            <el-switch v-model="widgetList[IndexActive].props.required" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { reactive, ref } from "vue";
import draggable from "vuedraggable";

interface option {
  [prop: string]: string | number;
}

interface module {
  name: string;
  componentName: string;
  icon: string;
  active: boolean;
  props: {
    label: string;
    placeholder: string;
    id: string;
    required: boolean;
    defaultValue: string | [] | number;
    [prop: string]: string | number | boolean | Array<option>;
  };
}
const dragList: module[] = reactive<module[]>([
  {
    componentName: "TextField",
    icon: "icon-xianxing",
    active: false,
    name: "单行输入框",
    props: {
      label: "单行输入框",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "TextAreaField",
    icon: "icon-dh",
    active: false,
    name: "多行输入框",
    props: {
      label: "多行输入框",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "NumberField",
    icon: "icon-sz",
    active: false,
    name: "数字输入框",
    props: {
      label: "数字输入框",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "RadioField",
    icon: "icon-danxuankuang",
    active: false,
    name: "单选框",
    props: {
      label: "单选框",
      placeholder: "请选择",
      id: "",
      required: true,
      defaultValue: "",
      options: [
        {
          label: "默认参数",
          value: 0,
        },
      ],
    },
  },
  {
    componentName: "CheckboxField",
    icon: "icon-dxk",
    active: false,
    name: "多选框",
    props: {
      label: "多选框",
      placeholder: "请选择",
      id: "",
      required: true,
      defaultValue: "",
      options: [
        {
          label: "默认参数",
          value: 0,
        },
      ],
    },
  },
  {
    componentName: "DatePickerField",
    icon: "icon-riqi",
    active: false,
    name: "日期",
    props: {
      label: "日期",
      placeholder: "请选择",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "DateRangePickerField",
    icon: "icon-rqqj",
    active: false,
    name: "日期区间",
    props: {
      label: "日期区间",
      placeholder: "请选择",
      id: "",
      required: true,
      defaultValue: "",
      startPlaceholder: "开始日期",
      endPlaceholder: "结束日期",
    },
  },
  {
    componentName: "ShuoMingField",
    icon: "icon-shuoming",
    active: false,
    name: "说明文字",
    props: {
      label: "说明文字",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "请输入说明文字",
    },
  },
  {
    componentName: "TextCardField",
    icon: "icon-sfz",
    active: false,
    name: "身份证",
    props: {
      label: "身份证",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "TextPhoneField",
    icon: "icon-shouji",
    active: false,
    name: "电话",
    props: {
      label: "电话",
      placeholder: "请输入",
      id: "",
      required: true,
      defaultValue: "",
    },
  },
  {
    componentName: "UploadFileField",
    icon: "icon-fujian",
    active: false,
    name: "附件",
    props: {
      label: "附件",
      placeholder: "",
      id: "",
      required: true,
      defaultValue: [],
    },
  },
  {
    componentName: "UploadImageField",
    icon: "icon-tp",
    active: false,
    name: "图片",
    props: {
      label: "图片",
      placeholder: "",
      id: "",
      required: true,
      defaultValue: [],
    },
  },
]);

const widgetList: module[] = reactive<module[]>([]);

let IndexActive = ref(0);
const changeActive = (item: module, index: number) => {
  widgetList.forEach((model: module) => {
    model.active = false;
  });
  item.active = true;
  IndexActive.value = index;
};

const getKeyRandom = () => {
  var text = "";
  var possible =
    "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
  for (var i = 0; i < 10; i++)
    text += possible.charAt(Math.floor(Math.random() * possible.length));
  return text;
};

const cloneDefaultField = (e: module) => {
  e.props.id = getKeyRandom()
  return JSON.parse(JSON.stringify(e));
};

//拖拽开始的事件
const onStart = (e: CustomEvent) => {
  console.log(e);
  console.log("开始拖拽");
};

//拖拽结束的事件
const onEnd = (e: CustomEvent) => {
  console.log(e);
  console.log("结束拖拽");
};

//拖拽开始的事件
const onStart2 = (e: CustomEvent) => {
  console.log(e);
  console.log("开始拖拽");
};

//拖拽结束的事件
const onEnd2 = (e: CustomEvent) => {
  console.log(e);
  console.log("结束拖拽");
};
</script>


<style scoped lang="scss">
.designer {
  height: 100vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  .header {
    z-index: 101;
    position: relative;
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 54px;
    background: #fff;
    box-shadow: 0 1px 4px 0 rgba(17, 31, 44, 0.06);
    .tabD {
      position: absolute;
      text-align: center;
      left: 0;
      right: 0;
      .tabs {
        position: relative;
        display: inline-block;
        align-items: center;
        justify-content: center;
        .tab {
          position: relative;
          display: inline-block;
          padding: 16px 16px;
          text-decoration: none;
          font-size: 14px;
          color: rgba(17, 31, 44, 0.56);
          cursor: pointer;
          .roundNumber {
            display: inline-block;
            color: #979797;
            background: #fff;
            border: 1px solid #979797;
            width: 14px;
            height: 14px;
            line-height: 14px;
            font-size: 12px;
            margin-right: 6px;
            border-radius: 50%;
          }
        }
        .tab.active {
          color: #0091ff;
          .roundNumber {
            font-weight: normal;
            color: #fff;
            background: #0091ff;
            border: 1px solid #0091ff;
          }
        }
      }
    }
  }
  .content {
    width: 100%;
    height: 100%;
    background: #fff;
    .left {
      width: 255px;
      background: #f5f6f6;
      border-right: 1px solid #ecedef;
      position: relative;
      padding: 0 12px;
      padding-top: 12px;
      height: 100%;
      display: inline-block;
      vertical-align: top;
      .title {
        display: flex;
        align-items: center;
        padding-bottom: 8px;
        font-weight: 500;
        padding-left: 2px;
        margin-right: 6px;
        font-family: PingFangSC-Medium;
        font-size: 12px;
        color: #171a1d;
      }
      .items {
        display: flex;
        flex-wrap: wrap;
        align-items: center;
        justify-content: space-between;
        .item {
          position: relative;
          width: 120px;
          height: 40px;
          display: flex;
          align-items: center;
          margin-bottom: 12px;
          border: 1px solid transparent;
          border-radius: 8px;
          cursor: grab;
          background-color: #fff;
          .unit {
            display: flex;
            align-items: center;
            width: 100%;
            height: 100%;
            i {
              margin: 0 8px;
            }
            span {
              display: flex;
              align-items: center;
              line-height: 16px;
              font-size: 12px;
              color: #111f2c;
            }
          }
        }
        .item:hover {
          border-color: #0089ff;
          box-shadow: 0 4px 8px 0 rgba(17, 31, 44, 0.08);
        }
      }
    }
    .panl {
      position: relative;
      width: calc(100% - 580px - 48px);
      max-height: calc(100vh - 200px);
      overflow-y: auto;
      display: inline-block;
      padding: 24px 24px 100px;
      vertical-align: top;
      .drag {
        display: flex;
        align-items: center;
        justify-content: center;
        padding-top: 4px;
        font-size: 14px;
        color: rgba(23, 26, 29, 0.4);
        img {
          height: 13px;
          margin-right: 6px;
        }
      }
      .item {
        position: relative;
        margin-bottom: 10px;
        border-left: 3px solid #fff;
        transition: 0.3s all ease;
        background: #fff;
        padding: 10px;
        .btn {
          position: absolute;
          right: -50px;
          display: none;
          z-index: 100;
          width: 50px;
          text-align: center;
          i:first-child {
            margin-right: 5px;
          }
          i {
            font-size: 15px;
            cursor: pointer;
          }
        }
      }
      .itemx {
        border-left: 3px solid #fff;
        margin-bottom: 10px;
      }
      .item::before {
        content: "";
        z-index: 99;
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        background: transparent;
      }
      .item:hover,
      .itemx:hover {
        z-index: 999;
        border-left: 3px solid #bfc1c2;
        box-shadow: 0 1px 10px 0 rgba(226, 226, 226, 0.5);
        cursor: grab;
      }
      .item[active="true"],
      .itemx[active="true"] {
        z-index: 999;
        border-left: 3px solid #0089ff !important;
        box-shadow: 0 1px 10px 0 rgba(226, 226, 226, 0.5);
        border-radius: 0;
        // padding-right: 50px;
        .btn {
          display: inline;
        }
      }
    }
    .right {
      width: 299px;
      background: #fff;
      border-left: 1px solid #ecedef;
      display: inline-block;
      vertical-align: top;
      height: 100%;
      .title {
        display: flex;
        align-items: center;
        height: 48px;
        padding: 0 12px;
        border-bottom: 1px solid #f0f0f0;
        i {
          margin-right: 4px;
        }
        span {
          font-size: 14px;
          color: #191f25;
          line-height: 22px;
        }
      }
      .list {
        padding: 12px;
        .item {
          margin-bottom: 24px;
          .label {
            display: flex;
            align-items: center;
            flex-wrap: wrap;
            margin-bottom: 6px;
            span {
              margin-right: 8px;
              font-size: 14px;
              display: inline-flex;
              align-items: center;
            }
            span:last-child {
              font-size: 12px;
              color: rgba(25, 31, 37, 0.4);
              line-height: 18px;
            }
          }
        }
      }
    }
  }
}
</style>

<style lang="scss">
.drag-content {
  min-height: 400px;
  // max-height: calc(100vh - 200px);
  // overflow-y: auto;
}
// .ghost {
//   height: 2px;
//   border-left: 2px solid #0089ff !important;
//   background: #0089ff !important;
//   padding: 0 !important;
//   margin-bottom: 10px !important;
//   *,
//   .el-form-item__label {
//     display: none !important;
//   }
// }
// .ghost:hover {
//   border-left: 2px solid #0089ff !important;
// }
</style>