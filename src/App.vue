<script setup>
import { onMounted, onUpdated, ref, unref } from 'vue';
import { cloneDeep } from 'lodash';
import sdkclass from 'blocksdk';

// Import Font Awesome CSS
import '@fortawesome/fontawesome-free/css/all.min.css';

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
    link: {
      type: 'text',
      placeholder: 'Image URL',
    },
    headline: {
      type: 'text',
      placeholder: 'Headline'
    },
    subheadline: {
      type: 'text',
      placeholder: 'Subheadline'
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
      placeholder: 'Button Link',
    },
    phone: {
      type: 'phone',
      placeholder: 'Phone'
    },
    email: {
      type: 'text',
      inputType: 'email',
      rules: [
        'required',
        'max:255',
        'email',
      ],
      placeholder: 'Email',
    },
    source: {
      type: 'text',
      placeholder: 'Source'
    },
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

<template>
  <Vueform :key="updateKey" v-bind="vueform" />

  <table id="widget-content" style="width: 100%; background-color: #f0f0f0; padding: 10px; border-radius: 5px; border-spacing: 10px;">
    <tr>
      <td colspan="2" style="text-align: left;">
        <h2 style="margin: 0 0 5px 0;">{{ formValues.headline }}</h2>
        <h4 style="margin: 0 0 10px 0; color: #555;">{{ formValues.subheadline }}</h4>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="padding: 0;">
        <template v-if="formValues.link">
          <img :src="formValues.link" alt="Image" style="width: 100%; height: auto; object-fit: cover;">
        </template>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="text-align: left; font-size: 0.9em; color: #888; padding: 0 0 10px 0;">
        <span v-if="formValues.source">{{ formValues.source }}</span>
      </td>
    </tr>
    <tr>
      <td colspan="2" style="vertical-align: top;">
        <div v-html="formValues.content" style="margin-bottom: 10px;"></div>
        <button v-if="formValues.button" style="display: block; width: 100%; padding: 10px; background-color: #007bff; color: #fff; border: none; border-radius: 5px; cursor: pointer;">
          {{ formValues.button }}
        </button>
      </td>
    </tr>
  </table>
</template>
