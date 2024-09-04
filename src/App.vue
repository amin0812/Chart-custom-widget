<template>
  <Vueform :key="updateKey" v-bind="vueform" />
  <div id="widget-content"
    style="max-width: 100%; margin: 0 auto; background-color: #f0f0f0; padding: 10px; border-radius: 5px;">
    <table style="width: 100%; border-collapse: collapse; table-layout: fixed;">
    <tr>
      <td class="regular-text" style="text-align: left; padding: 10px 100px;">
        <h2 class="h3-purple" style="color:#6D5BA3; margin-bottom: 5px;">
          {{ formValues.headline }}
        </h2>
        <h4 class="h4-headline" style="color:#181E20; margin: 0;">
          {{ formValues.subheadline }}
        </h4>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="padding: 0;">
        <template v-if="formValues.link">
          <img :src="formValues.link" alt="Image" :style="{
    width: imageSize,
    height: 'auto',
    objectFit: 'cover',
    display: 'block',
    margin: imageAlignment
  }">
        </template>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="text-align: left; font-size: 0.9em; color: #888; padding: 0 40px 10px;">
        <span v-if="formValues.source">{{ formValues.source }}</span>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="vertical-align: top; padding: 0 40px;">
        <div v-html="formValues.content" :style="{ width: imageSize, margin: '0 auto', textAlign: 'left', paddingBottom:'20px', margin: imageAlignment }"></div>

        <template v-if="formValues.button">
          <div :style="{ width: imageSize, margin: imageAlignment, paddingBottom: '20px' }">
            <table border="0" cellpadding="0" cellspacing="0" style="background:#ffffff; width: 100%;">
              <tr>
                <td width="28px !important;">
                  <img alt="" height="40"
                    src="https://image.s50.sfmc-content.com/lib/fe3211717564047b7c1272/m/1/2cff3a47-c908-46e5-bf25-fcc44541a4d3.png"
                    style="display: block; height: 40px !important; max-height: 40px !important;" width="28">
                </td>
                <td style="background-color: #007d85;">
                  <a :href="formValues.buttonLink"
                    style="color:#ffffff;text-decoration:none;font-family: DWS Slab TT, Rockwell, Calibri, Arial, Helvetica, sans-serif;height:40px !important;max-height:40px !important;font-size: 14px;line-height: 45px;mso-line-height-rule: exactly;display: block;background: #007d85;background-color: #007d85;text-align: center;vertical-align: middle;"
                    :title="formValues.button">{{ formValues.button }}</a>
                </td>
                <td width="28px !important;">
                  <img alt="" height="40"
                    src="https://image.s50.sfmc-content.com/lib/fe3211717564047b7c1272/m/1/1d91c65d-47fb-4c8a-98e1-612c093749f7.png"
                    style="display: block; height: 40px !important; max-height: 40px !important;" width="28">
                </td>
              </tr>
            </table>
          </div>
        </template>
      </td>
    </tr>
  </table>
  </div>
</template>

<script setup>
import { onMounted, onUpdated, ref, computed, unref } from 'vue';
import { cloneDeep } from 'lodash';
import sdkclass from 'blocksdk';

const sdk = new sdkclass();

const formValues = ref({});

const vueform = ref({
  size: 'md',
  displayErrors: false,
  onChange: (Values) => { formValues.value = Values },
  schema: {
    title: {
      type: 'static',
      content: 'Custom Widget',
      tag: 'h1'
    },
    headline: {
      type: 'text',
      placeholder: 'Headline'
    },
    subheadline: {
      type: 'text',
      placeholder: 'Subheadline'
    },
    link: {
      type: 'text',
      placeholder: 'Image URL',
    },
    content: {
      type: 'editor',
    },
    separation: {
      type: 'static',
      tag: 'hr',
    },
    button: {
      type: 'text',
      placeholder: 'Button Name',
    },
    buttonLink: {
      type: 'text',
      placeholder: 'Button Link',
    },
    source: {
      type: 'text',
      placeholder: 'Source'
    },
    ImageSize: {
      type: 'radiogroup',
      items: [
        'Small',
        'Medium',
        'Large',
        'Default'
      ],
      label: 'Image Size',
    },
    ImageAlignment: {
      type: 'radiogroup',
      items: [
        'Left',
        'Center',
        'Right',
      ],
      label: 'Image Alignment',
    },
  }
});

// Computed property to determine image size based on selected option
const imageSize = computed(() => {
  switch (formValues.value.ImageSize) {
    case 'Small':
      return '100px';
    case 'Medium':
      return '200px';
    case 'Large':
      return '300px';
    default:
      return '100%'; // Default or fallback size
  }
});

// Computed property to determine image alignment based on selected option
const imageAlignment = computed(() => {
  switch (formValues.value.ImageAlignment) {
    case 'Left':
      return '0 auto 0 0'; // Left align
    case 'Right':
      return '0 0 0 auto'; // Right align
    case 'Center':
      return '0 auto'; // Center align
    default:
      return '0 auto'; // Default to center
  }
});

onMounted(() => {
  sdk.getData((data) => {
    formValues.value = data;
    vueform.value.default = data;
    updateKey.value++;
  });
});

onUpdated(() => {
  const content = document.querySelector("#widget-content").innerHTML;
  sdk.setData(cloneDeep(unref(formValues)));
  sdk.setContent(content);
});

const updateKey = ref(0);
</script>
