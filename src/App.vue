<template>
  <div id="app">
    <div class="container">
      <h1 class="title">
        地面采样距离(GSD)计算器
      </h1>

      <div>
        <label style="height: 36px; line-height: 36px;">常用参数选择</label>
        <a-select style="width: 100%;" @change="cameraSelected">
          <a-select-opt-group v-for="(group, i) in cameras" :key="'g' + i">
            <span slot="label">{{ group.groupName }}</span>
            <a-select-option
              v-for="(camera, j) in group.cameras"
              :key="'g' + i + 'i' + j"
              :value="i + '-' + j"
            >
              {{ camera.name }}
            </a-select-option>
          </a-select-opt-group>
        </a-select>
      </div>

      <div class="separate"></div>

      <div class="camera-parameter">
        <h1 style="font-size: 1.4rem;">相机参数</h1>

        <div class="sensor">
          <h1 style="font-size: 1rem;">传感器尺寸(mm)</h1>
          <a-row>
            <a-col :span="6" class="label-after" style="text-align: end;"
              ><label style="height: 36px; line-height: 36px;">宽</label></a-col
            >
            <a-col :span="6">
              <a-input-number v-model="camera.sensor.width"
            /></a-col>
            <a-col :span="6" class="label-after" style="text-align: end;"
              ><label style="height: 36px; line-height: 36px;">高</label></a-col
            >
            <a-col :span="6">
              <a-input-number v-model="camera.sensor.height"
            /></a-col>
          </a-row>
        </div>

        <div class="resolution">
          <h1 style="font-size: 1rem;">分辨率</h1>
          <a-row>
            <a-col :span="6" class="label-after" style="text-align: end;"
              ><label style="height: 36px; line-height: 36px;">宽</label></a-col
            >
            <a-col :span="6">
              <a-input-number v-model="camera.resolution.width"
            /></a-col>

            <a-col :span="6" class="label-after" style="text-align: end;"
              ><label style="height: 36px; line-height: 36px;">高</label></a-col
            >
            <a-col :span="6">
              <a-input-number v-model="camera.resolution.height"
            /></a-col>
          </a-row>
        </div>
      </div>

      <div class="photograph-parameter">
        <h1 style="font-size: 1.4rem;">拍摄参数</h1>

        <a-row>
          <a-col :span="6" class="label-after" style="text-align: end;"
            ><label style="height: 36px; line-height: 36px;">焦距</label></a-col
          >

          <a-col :span="18">
            <a-input-number v-model="focalLength" /><label
              style="height: 36px; line-height: 36px;"
              >mm</label
            >
          </a-col>

          <!-- <a-col
            :span="6"
            class="label-after"
            style="text-align: end; display: none;"
            ><label style="height: 36px; line-height: 36px;"
              >像片比例</label
            ></a-col
          >

          <a-col :span="6" style="display: none;">
            <a-select style="width: 88px;" @change="aspectRatioSelected">
              <a-select-option
                v-for="aspectRatio in aspectRatios"
                :key="aspectRatio.value"
                :value="aspectRatio.value"
              >
                {{ aspectRatio.name }}
              </a-select-option>
            </a-select>
          </a-col> -->
        </a-row>

        <a-row style="margin-top: 1rem;">
          <a-col
            :span="6"
            class="label-after"
            style="height: 36px; text-align: end;"
          >
            <label style="height: 36px; line-height: 36px;">航高(m)</label>
          </a-col>
          <a-col :span="12" style="padding-right: 30px; margin-top: 16px">
            <a-slider v-model="flyingHeight" :min="1" :max="1000" />
          </a-col>
          <a-col :span="6">
            <a-input-number v-model="flyingHeight" :min="1" :max="1000" />
          </a-col>
        </a-row>
      </div>

      <div class="separate"></div>

      <div class="result">
        <a-row>
          <a-col
            :xs="16"
            :sm="16"
            :md="12"
            class="label-after"
            style="text-align: end;"
          >
            <label class="result-label">地面采样距离GSD(cm):</label>
          </a-col>

          <a-col :xs="8" :sm="8" :md="12">
            <label class="result-value">{{ gsd }}</label>
          </a-col>
        </a-row>
      </div>

      <a-button type="primary" @click="ok" style="display: none;">
        确定
      </a-button>
    </div>
  </div>
</template>

<script>
import { Button, Row, Col, Slider, InputNumber, Select } from 'ant-design-vue'
export default {
  name: 'App',
  data() {
    return {
      flyingHeight: 100,
      camera: {
        sensor: {
          width: null,
          height: null
        },
        resolution: {
          width: null,
          height: null
        }
      },
      aspectRatio: null,
      focalLength: null,

      cameras: [
        {
          groupName: '大疆',
          cameras: [
            {
              sensor: {
                width: 13.2,
                height: 8.8
              },
              resolution: {
                width: 5472,
                height: 3648
              },
              focalLength: 8.8,
              name: '大疆精灵4RTK（FC6310R）'
            },
            {
              sensor: {
                width: 7.4,
                height: 5.6
              },
              resolution: {
                width: 5184,
                height: 3888
              },
              focalLength: 80,
              name: '大疆禅思ZenmuseH20主相机（变焦相机）'
            }
          ]
        },
        {
          groupName: '赛尔',
          cameras: [
            {
              sensor: {
                width: 23.5,
                height: 15.6
              },
              resolution: {
                width: 6000,
                height: 4000
              },
              focalLength: 35,
              name: '赛尔102S(定焦)'
            },
            {
              sensor: {
                width: 35.9,
                height: 24
              },
              resolution: {
                width: 7952,
                height: 5304
              },
              focalLength: 40,
              name: '赛尔202S下视'
            },
            {
              sensor: {
                width: 35.7,
                height: 23.8
              },
              resolution: {
                width: 9504,
                height: 6336
              },
              focalLength: 40,
              name: '赛尔6100(40/56mm定焦)'
            }
          ]
        }
      ],
      aspectRatios: [
        {
          value: 0,
          aspectRatio: 1.3333333333333333,
          name: '4:3'
        },
        {
          value: 1,
          aspectRatio: 1.5,
          name: '3:2'
        }
      ]
    }
  },
  components: {
    AButton: Button,
    ARow: Row,
    ACol: Col,
    ASlider: Slider,
    AInputNumber: InputNumber,
    ASelect: Select,
    ASelectOption: Select.Option,
    ASelectOptGroup: Select.OptGroup
  },
  computed: {
    gsd() {
      if (
        this.flyingHeight &&
        this.camera.sensor.width &&
        this.camera.sensor.height &&
        this.camera.resolution.width &&
        this.camera.resolution.height &&
        // this.aspectRatio &&
        this.focalLength
      ) {
        const pixelWidth =
          (this.camera.sensor.width * 1000) / this.camera.resolution.width
        const pixelHeight =
          (this.camera.sensor.height * 1000) / this.camera.resolution.height
        const pixelSize = pixelWidth > pixelHeight ? pixelHeight : pixelWidth

        // console.log(pixelSize)
        // const cameraAspectRatio = this.camera.resolution.width / this.camera.resolution.height

        return (
          (((this.flyingHeight * 1000) / this.focalLength) * pixelSize) /
          10000
        ).toFixed(3)
      }
      return 0
    }
  },
  methods: {
    cameraSelected(value) {
      const indexes = value.split('-')
      const camera = this.cameras[indexes[0]].cameras[indexes[1]]
      this.camera.sensor.width = camera.sensor.width
      this.camera.sensor.height = camera.sensor.height
      this.camera.resolution.width = camera.resolution.width
      this.camera.resolution.height = camera.resolution.height
      this.focalLength = camera.focalLength
    },
    aspectRatioSelected(value) {
      const aspectRatio = this.aspectRatios[value]
      this.aspectRatio = aspectRatio.aspectRatio
    },

    ok() {
      console.log('fly height', this.flyingHeight)
      console.log('camera', this.camera)
      console.log('aspectRatio', this.aspectRatio)
    }
  }
}
</script>

<style>
.jt-gsd-container {
  display: flex;
  justify-content: center;
  align-items: center;
  background: #fff url('./assets/r.jpg') no-repeat center;
  background-size: cover;
  width: 100%;
  height: 100%;
  z-index: 0;
}
</style>
<style scoped>
* {
  margin: 0;
  padding: 0;
  font-family: 'Avenir Next', 'Helvetica Neue', sans-serif, Arial,
    'Source Sans Pro', -apple-system, Roboto, BlinkMacSystemFont, 'Segoe UI' !important;
  font-size: 16px;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
  color: #000;
}

.title {
  font-size: 1.8rem;
  text-align: center;
}

#app {
  width: 960px;
  height: 100%;
  display: flex;
  position: relative;
  z-index: 1;
}

#app::before {
  content: '';
  position: absolute;
  background-color: #fff;
  background: rgba(255 255 255 /0.75);
  filter: blur(3rem);
  z-index: -1;
  width: 100%;
  height: 100%;
  top: 0px;
  left: 0px;
  overflow: hidden;
}

.container {
  margin: auto;
  max-width: 960px;
  width: 100%;
  padding: 30px;
  background: rgba(255 255 255 /0.85);
  border-radius: 1rem;
  -webkit-box-shadow: #666 0px 0px 10px;
  -moz-box-shadow: #666 0px 0px 10px;
  box-shadow: #666 0px 0px 10px;
}

.label-after {
  padding-right: 1rem;
}

.result {
  margin-top: 30px;
}

.result-label {
  height: 36px;
  line-height: 36px;
  font-size: 1.4rem;
}

.result-value {
  height: 36px;
  line-height: 36px;
  font-size: 1.4rem;
}

.separate {
  width: 100%;
  height: 2px;
  background-color: rgba(220 220 220 /1);
  margin-top: 10px;
  margin-bottom: 10px;
}

@media (max-width: 960px) {
  .container {
    padding: 30px;
    border-radius: 0;
  }
}
@media (max-width: 660px) {
  .container {
    padding: 10px;
    border-radius: 0;
  }
  .result-label {
    font-size: 1.1rem;
    font-weight: bold;
  }
  .result-value {
    font-size: 1.1rem;
    font-weight: bold;
  }
}
@media (max-width: 500px) {
  .title {
    font-size: 1.4rem;
    text-align: center;
  }
}
</style>
