<template>
  <div class="set-type">
    <n-h3 prefix="bar"> 歌词 </n-h3>
    <n-collapse>
      <n-collapse-item title="字体设置">
        <n-card class="set-item" :content-style="{
            flexDirection: 'column',
            alignItems: 'flex-start',
          }">
          <div class="name">
            歌词文本大小
            <n-text :style="{ fontSize: lyricsFontSize + 'px', fontWeight: 'bold' }" class="tip">
              歌词显示测试
            </n-text>
          </div>
          <n-slider v-model:value="lyricsFontSize" :tooltip="false" :max="56" :min="25" :step="1" :marks="{
              25: '最小',
              36: '默认',
              56: '最大',
            }" />
        </n-card>
        <n-card class="set-item">
          <div class="name">
            歌词字体
            <n-text class="tip">选择您偏好的字体</n-text>
          </div>
          <n-select
            v-model:value="lyricsFont"
            :options="[
              {
                label: 'LXGW WenKai',
                value: 'LXGW WenKai',
              },
              {
                label: 'HarmonyOS Sans',
                value: 'HarmonyOS Sans', 
              },
              {
                label: 'PingFang SC',
                value: 'PingFang SC',
              },
            ]"
            class="set"
            @update:value="updateLyricsFont"
          />
        </n-card>
        <n-card class="set-item">
          <div class="name">
            歌词字体加粗
            <n-text class="tip">是否将歌词字体加粗显示，部分字体可能显示异常</n-text>
          </div>
          <n-switch v-model:value="lyricsBold" :round="false" />
        </n-card>
      </n-collapse-item>
      <n-collapse-item title="显示设置">
        <n-card class="set-item">
          <div class="name">
            歌词位置
            <n-text class="tip">歌词的默认垂直位置</n-text>
          </div>
          <n-select v-model:value="lyricsPosition" :options="[
              {
                label: '居左',
                value: 'left',
              },
              {
                label: '居中',
                value: 'center',
              },
              {
                label: '居右',
                value: 'right',
              },
            ]" class="set" />
        </n-card>
        <n-card class="set-item">
          <div class="name">
            歌词滚动位置
            <n-text class="tip">歌词高亮时所处的位置</n-text>
          </div>
          <n-select 
            :options="[
              {
                label: '靠近顶部',
                value: 'start',
              },
              {
                label: '水平居中', 
                value: 'center',
              },
            ]"
            :value="lyricsBlock"
            @update:value="(val) => {
              lyricsBlock = val;
              alignAnchor = val === 'start' ? 'top' : 'center';
            }"
            class="set" 
          />
        </n-card>
        <n-card class="set-item">
          <div class="name">
            智能暂停滚动
            <n-text class="tip">鼠标移入歌词区域是否暂停滚动</n-text>
          </div>
          <n-switch v-model:value="lrcMousePause" :round="false" />
        </n-card>
      </n-collapse-item>
    <n-collapse-item title="歌词类型设置">
      <n-card class="set-item">
        <div class="name">
          <div class="dev">
            显示逐字歌词
          </div>
          <n-text class="tip">是否在具有逐字歌词时显示</n-text>
        </div>
        <n-switch v-model:value="showYrc" :disabled="useTTMLFormat" :round="false" />
      </n-card>
      <n-card class="set-item">
        <div class="name">
          <div class="dev">
            显示逐字歌词动画
          </div>
          <n-text class="tip">可能会造成卡顿等性能问题，手机端建议关闭</n-text>
        </div>
        <n-switch v-model:value="showYrcAnimation" :disabled="!showYrc" :round="false" />
      </n-card>
      <n-card class="set-item">
        <div class="name">
          显示歌词翻译
          <n-text class="tip">是否在具有翻译歌词时显示</n-text>
        </div>
        <n-switch v-model:value="showTransl" :disabled="useAMLyrics" :round="false" />
      </n-card>
      <n-card class="set-item">
        <div class="name">
          显示歌词音译
          <n-text class="tip">是否在具有音译歌词时显示</n-text>
        </div>
        <n-switch v-model:value="showRoma" :disabled="useAMLyrics" :round="false" />
      </n-card>
    </n-collapse-item>
    <n-collapse-item title="歌词渲染设置">
      <n-card class="set-item">
        <n-collapse>
          <n-collapse-item title="Apple Music Like Lyrics 设置">
            <div class="name">
              使用Apple Music-Like Lyrics渲染器
              <n-text class="tip">
                Apple Music-Like Lyrics渲染器可能会造成卡顿等性能问题，手机端建议关闭
              </n-text>
            </div>
            <n-switch v-model:value="useAMLyrics" :round="false" />
            <div class="name" style="margin-top: 16px;">
              使用AMLL弹簧渲染效果
              <n-text class="tip">
                开启本项会使用Apple Music-Like Lyrics组件的弹簧效果
              </n-text>
            </div>
            <n-switch v-model:value="useAMSpring" :round="false" />
            <div class="name" style="margin-top: 16px;">
              <div class="dev">
                使用TTML格式歌词
                <n-tag :bordered="false" round size="small" type="warning">
                  开发中
                  <template #icon>
                    <n-icon>
                      <SvgIcon icon="code" />
                    </n-icon>
                  </template>
                </n-tag>
              </div>
              <n-text class="tip">
                开启本项后，歌词将使用TTML格式歌词替换原有的LRC格式歌词, LCY Music Pro 将尝试从 Internet 获取TTML歌词, 若获取失败则仍使用LRC格式歌词
              </n-text>
            </div>
            <n-switch v-model:value="useTTMLFormat" :disabled="!useAMLyrics" :round="false" />
          </n-collapse-item>
        </n-collapse>
        <n-card class="set-item">
        <div class="name">
          弹簧动画参数
          <span class="tip">调整歌词动画的弹性效果</span>
        </div>
        <n-collapse>
          <n-collapse-item title="横向移动">
            <n-form-item label="质量">
              <n-input-number v-model:value="springParams.posX.mass" :min="0.1" :step="0.1" />
            </n-form-item>
            <n-form-item label="阻尼">
              <n-input-number v-model:value="springParams.posX.damping" :min="0" :step="1" />
            </n-form-item>
            <n-form-item label="刚度">
              <n-input-number v-model:value="springParams.posX.stiffness" :min="0" :step="1" />
            </n-form-item>
          </n-collapse-item>
          <n-collapse-item title="纵向移动">
            <n-form-item label="质量">
              <n-input-number v-model:value="springParams.posY.mass" :min="0.1" :step="0.1" />
            </n-form-item>
            <n-form-item label="阻尼">
              <n-input-number v-model:value="springParams.posY.damping" :min="0" :step="1" />
            </n-form-item>
            <n-form-item label="刚度">
              <n-input-number v-model:value="springParams.posY.stiffness" :min="0" :step="1" />
            </n-form-item>
          </n-collapse-item>
          <n-collapse-item title="缩放">
            <n-form-item label="质量">
              <n-input-number v-model:value="springParams.scale.mass" :min="0.1" :step="0.1" />
            </n-form-item>
            <n-form-item label="阻尼">
              <n-input-number v-model:value="springParams.scale.damping" :min="0" :step="1" />
            </n-form-item>
            <n-form-item label="刚度">
              <n-input-number v-model:value="springParams.scale.stiffness" :min="0" :step="1" />
            </n-form-item>
          </n-collapse-item>
        </n-collapse>
      </n-card>
      </n-card>
      <n-card class="set-item">
        <div class="name">
          歌词自动聚焦
          <n-text class="tip">是否聚焦显示当前播放行，其他行将模糊显示, 手机端建议关闭</n-text>
        </div>
        <n-switch v-model:value="lyricsBlur" :round="false" />
      </n-card>
    </n-collapse-item>
  </n-collapse>
    <div v-if="isElectron" class="set-list">
      <n-h3 prefix="bar"> 桌面歌词 </n-h3>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">开启桌面歌词</n-text>
          <n-text class="tip" :depth="3"> 如遇问题请向开发者反馈 </n-text>
        </div>
        <n-switch
          :value="statusStore.showDesktopLyric"
          :round="false"
          class="set"
          @update:value="player.toggleDesktopLyric"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">桌面歌词文字大小</n-text>
          <n-text class="tip" :depth="3">翻译或其他文字将会跟随变化</n-text>
        </div>
        <n-select
          v-model:value="desktopLyricConfig.fontSize"
          :options="
            Array.from({ length: 41 }, (_, i) => {
              return {
                label: `${20 + i} px`,
                value: 20 + i,
              };
            })
          "
          class="set"
          @update:value="saveDesktopLyricConfig"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">主题色</n-text>
          <n-text class="tip" :depth="3">桌面歌词文字主色</n-text>
        </div>
        <n-color-picker
          v-model:value="desktopLyricConfig.mainColor"
          :show-alpha="false"
          :modes="['hex']"
          class="set"
          @complete="saveDesktopLyricConfig"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">描边色</n-text>
          <n-text class="tip" :depth="3">桌面歌词文字描边色</n-text>
        </div>
        <n-color-picker
          v-model:value="desktopLyricConfig.shadowColor"
          :modes="['rgb']"
          class="set"
          @complete="saveDesktopLyricConfig"
        />
      </n-card>
      <n-card class="set-item">
        <div class="label">
          <n-text class="name">恢复默认配置</n-text>
          <n-text class="tip" :depth="3">恢复默认桌面歌词配置</n-text>
        </div>
        <n-button type="primary" @click="restoreDesktopLyricConfig">恢复默认</n-button>
      </n-card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { useSettingStore, useStatusStore } from "@/stores";
import { cloneDeep, isEqual } from "lodash-es";
import { isElectron } from "@/utils/helper";
import player from "@/utils/player";
import { openLyricExclude } from "@/utils/modal";

const statusStore = useStatusStore();
const settingStore = useSettingStore();

// 桌面歌词配置
const defaultDesktopLyricConfig = {
  fontSize: 30,
  mainColor: "#fff",
  shadowColor: "rgba(0, 0, 0, 0.5)",
};
const desktopLyricConfig = reactive({ ...defaultDesktopLyricConfig });

// 获取桌面歌词配置
const getDesktopLyricConfig = async () => {
  if (!isElectron) return;
  const config = await window.electron.ipcRenderer.invoke("get-desktop-lyric-option");
  if (config) Object.assign(desktopLyricConfig, config);
  // 监听更新
  window.electron.ipcRenderer.on("desktop-lyric-option-change", (_, config) => {
    if (config && !isEqual(desktopLyricConfig, config)) {
      Object.assign(desktopLyricConfig, config);
    }
  });
};

// 保存桌面歌词配置
const saveDesktopLyricConfig = () => {
  try {
    if (!isElectron) return;
    console.log(cloneDeep(desktopLyricConfig));
    window.electron.ipcRenderer.send(
      "set-desktop-lyric-option",
      cloneDeep(desktopLyricConfig),
      true,
    );
    window.$message.success("桌面歌词配置已保存");
  } catch (error) {
    console.error("Failed to save options:", error);
    window.$message.error("桌面歌词配置保存失败");
    getDesktopLyricConfig();
  }
};

// 恢复默认桌面歌词配置
const restoreDesktopLyricConfig = () => {
  try {
    if (!isElectron) return;
    window.electron.ipcRenderer.send("set-desktop-lyric-option", defaultDesktopLyricConfig, true);
    window.$message.success("桌面歌词配置已恢复默认");
    console.log(defaultDesktopLyricConfig, desktopLyricConfig);
  } catch (error) {
    console.error("Failed to save options:", error);
    window.$message.error("桌面歌词配置恢复默认失败");
    getDesktopLyricConfig();
  }
};

onMounted(() => {
  getDesktopLyricConfig();
});
</script>

<style lang="scss" scoped>
#lyrics-show {
  .lrc-item {
    display: flex;
    flex-direction: column;
    opacity: 0.3;
    transform-origin: var(--transform-origin);
    transform: scale(0.86);
    transition: all 0.3s;
    &.on {
      opacity: 1;
      transform: scale(1);
    }
    .n-text {
      &:nth-of-type(1) {
        font-weight: var(--font-weight);
        font-size: calc(var(--font-size) * 1px);
      }
      &:nth-of-type(2) {
        opacity: 0.6;
        font-size: calc(var(--font-tran-size) * 1px);
      }
      &:nth-of-type(3) {
        opacity: 0.6;
        font-size: calc(var(--font-roma-size) * 1px);
      }
    }
  }
}
</style>

<script setup>
import { storeToRefs } from "pinia";
import { watch } from "vue";
import { siteSettings } from "@/stores";

const settings = siteSettings();
const {
  alignAnchor,
  showYrc,
  showYrcAnimation,
  showTransl,
  showRoma,
  lyricsPosition,
  lyricsBlock,
  lrcMousePause,
  lyricsFontSize,
  lyricsBlur,
  lyricsBold,
  useAMLyrics,
  useAMSpring,
  lyricsFont,
  springParams,
  useTTMLFormat,
} = storeToRefs(settings);

// 监听TTML格式开关状态
watch(useTTMLFormat, (newVal) => {
  if (newVal) {
    showYrc.value = true;
  }
});

watch(useAMLyrics, (newVal) => {
  if (newVal === false) {
    useTTMLFormat.value = false;
  }
});

// 更新全局歌词字体
const updateLyricsFont = () => {
  document.documentElement.style.setProperty('--main-font-family-lyric', `"${settings.lyricsFont}", system-ui, -apple-system, sans-serif`);
}
</script>

<style lang="scss" scoped>
.set-type {
  .n-collapse {
    background-color: transparent;
    border: none;

    :deep(.n-collapse-item) {
      margin-bottom: 16px;
      border: none;

      .n-collapse-item__header {
        font-size: 16px;
        font-weight: bold;
        border: none;
        background-color: transparent;
      }

      .n-collapse-item__content-wrapper {
        border: none;
      }

      .n-collapse-item__content-inner {
        padding: 8px 0;
      }
    }
  }

  .set-item {
    margin-bottom: 12px;
    background-color: rgba(255, 255, 255, 0.05);
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 8px;

    @media screen and (max-width: 768px) {
      margin-bottom: 8px;
      padding: 12px !important;
    }

    .name {
      margin-bottom: 8px;
      font-size: 14px;

      @media screen and (max-width: 768px) {
        font-size: 13px;
        margin-bottom: 6px;
      }

      .tip {
        display: block;
        margin-top: 4px;
        font-size: 12px;
        opacity: 0.6;

        @media screen and (max-width: 768px) {
          font-size: 11px;
          margin-top: 2px;
        }
      }
    }

    .set {
      width: 100%;
    }

    :deep(.n-form-item) {
      margin-bottom: 12px;
      
      @media screen and (max-width: 768px) {
        margin-bottom: 8px;
      }

      .n-form-item-label {
        font-size: 14px;
        padding-bottom: 4px;

        @media screen and (max-width: 768px) {
          font-size: 13px;
          padding-bottom: 2px;
        }
      }

      .n-input-number {
        width: 100%;
        max-width: 200px;
        background-color: rgba(255, 255, 255, 0.08);
        border: 1px solid rgba(255, 255, 255, 0.1);
        border-radius: 4px;

        @media screen and (max-width: 768px) {
          max-width: none;
          padding: 4px 8px;
          margin-top: 4px;
        }

        &:hover, &:focus {
          border-color: rgba(255, 255, 255, 0.2);
        }

        .n-input-wrapper {
          padding: 0 8px;

          @media screen and (max-width: 768px) {
            padding: 0 4px;
          }
        }

        .n-input__input {
          height: 32px;
          padding: 0;
          color: rgba(255, 255, 255, 0.9);

          @media screen and (max-width: 768px) {
            height: 36px;
            font-size: 14px;
          }
        }

        .n-input-number-suffix, 
        .n-input-number-prefix {
          color: rgba(255, 255, 255, 0.5);

          @media screen and (max-width: 768px) {
            font-size: 12px;
          }
        }
      }
    }

    :deep(.n-collapse) {
      @media screen and (max-width: 768px) {
        .n-collapse-item__header {
          padding: 8px;
          font-size: 14px;
        }

        .n-collapse-item__content-inner {
          padding: 8px;
        }
      }
    }

    :deep(.n-switch) {
      @media screen and (max-width: 768px) {
        height: 24px;
        min-width: 44px;
      }
    }

    :deep(.n-tag) {
      @media screen and (max-width: 768px) {
        font-size: 11px;
        padding: 0 6px;
      }
    }
  }
}
</style>
