**Event事件部分派生关系**
![图片描述](http://img.mukewang.com/climg/60360d9a09d1d62a22711401.jpg)

**ImageProcesser.vue组件**
```javascript
<template>
  <div class="image-processer">
    <div class="image-preview" :style="{backgroundImage: `url('${value}')`}"/>
    <div class="image-process">
      <uploader
        class="uploader"
        action="http://123.57.138.48/api/upload/"
        :showUploadList="false"
        :beforeUpload="commonUploadCheck"
        @success="(data) => {handleUploadSuccess(data.resp, data.file.raw)}"
      >
        <div class="uploader-container">
          <a-button shape="round">
            <template #icon>
              <UploadOutlined/>
            </template>
            更换图片
          </a-button>
        </div>
        <template #loading>
          <div class="uploader-container">
            <a-button shape="round">
              <template #icon>
                <LoadingOutlined/>
              </template>
              上传中
            </a-button>
          </div>
        </template>
        <template #uploaded>
          <div class="uploader-container">
            <a-button shape="round">
              <template #icon>
                <UploadOutlined/>
              </template>
              更换图片
            </a-button>
          </div>
        </template>
      </uploader>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import Uploader from './Uploader.vue';
import { commonUploadCheck } from '../helper';
import { UploadOutlined, LoadingOutlined } from '@ant-design/icons-vue';
import { UploadResp } from '../../types/UploadResp';

export default defineComponent({
  name: 'ImageProcesser',
  components: {
    Uploader,
    UploadOutlined,
    LoadingOutlined
  },
  props: {
    value: {
      type: String as PropType<string>,
      required: true
    }
  },
  emits: ['change'],
  setup(props, {emit}) {
    const handleUploadSuccess = (resp: UploadResp, file: File) => {
      emit('change', resp.data.url);
    };
    return {
      commonUploadCheck,
      handleUploadSuccess
    };
  }
});
</script>

<style lang="scss" scoped>
.image-processer {
  padding: 10px 0;
  display: flex;
  box-sizing: border-box;

  .image-preview {
    flex: 0 0 150px;
    width: 150px;
    height: 84px;
    border: 1px dashed #e6ebed;
    background: no-repeat 50%/contain;
  }

  .image-process {
    padding: 5px 0;
    margin-left: 10px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
  }
}
</style>

```
**测试**
ImageProcesser.spec.ts
```javascript
import { mount, VueWrapper } from '@vue/test-utils';
import ImageProcesser from '@/components/ImageProcesser.vue';
import flushPromises from 'flush-promises';
import axios from 'axios';

jest.mock('axios');
const mockedAxios = axios as jest.Mocked<typeof axios>;

let wrapper: VueWrapper<any>;
// 测试文件
const testFile = new File(['xyz'], 'test.png', {type: 'image/png'});
const mockComponent = {
  template: '<div><slot></slot></div>'
};
const mockButtonComponent = {
  template: '<button><slot></slot></button>'
};
const mockComponents = {
  'UploadOutlined': mockComponent,
  'LoadingOutlined': mockComponent,
  'a-button': mockButtonComponent
};
//  设置文件的值
const setInputValue = (input: HTMLInputElement) => {
  const files = [testFile] as any;
  Object.defineProperty(input, 'files', {
    value: files,
    writable: false
  });
};
describe('ImageProcesser', () => {
  beforeAll(() => {
    wrapper = mount(ImageProcesser, {
      props: {
        value: 'test.url'
      },
      global: {
        stubs: mockComponents
      }
    });
  });

  it('测试图片是否显示', () => {
    expect(wrapper.find('.image-preview').attributes('style')).toBe('background-image: url(test.url);');
  });
  it('测试更改图片', async () => {
    mockedAxios.post.mockResolvedValueOnce({data: {url: 'a.png'}});
    const vm = wrapper.vm as any;
    vm.handleUploadSuccess = jest.fn();
    expect(wrapper.get('button').text()).toBe('更换图片');
    const input = wrapper.get('input').element as HTMLInputElement;
    setInputValue(input);
    await wrapper.get('input').trigger('change');
    expect(mockedAxios.post).toBeCalledTimes(1);
    expect(wrapper.get('button').text()).toBe('上传中');
    await flushPromises();
    expect(wrapper.get('button').text()).toBe('更换图片');
    expect(vm.handleUploadSuccess).toBeCalled();
    expect(vm.handleUploadSuccess).toBeCalledWith({
      url: 'a.png'
    }, testFile);
    await wrapper.setProps({
      value: 'a.png'
    });
    expect(wrapper.find('.image-preview').attributes('style')).toBe('background-image: url(a.png);');
  });
  afterEach(() => {
    mockedAxios.post.mockReset();
  });
});
```
**集成到属性面板**
- 步骤一
  更改PropsToForms.ts
  ```javascript
  type AllProps = TextComponentProps & ImageComponentProps;
  type PropsToForms = {
    [key in keyof AllProps]?: PropToForm;
  }
  ```
- 步骤二
  修改propsMap.ts
  ```javascript
   src: {
      component: 'image-processer',
      text: ''
    }
  ```

**效果图**
![图片描述](http://img.mukewang.com/climg/603602fa09e366b833461734.jpg)
