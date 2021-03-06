<template>
  <admin-container>
    <section class="p-6">
      <form :action="`/admin/items/${item.id}/update`" method="post" autocomplete="off" novalidate>
        <errors-block :errors="errors"/>
        <div class="flex flex-wrap flex-row-reverse lg:flex-row mb-4">
          <div class="w-full lg:w-3/5 px-2">
            <brick>
              <div class="mb-4">
                <form-label :show="true" for="Title">
                  Title
                </form-label>
                <text-field id="Title" type="text" name="title" :value="title" :error="fields.includes('title')" @onInput="createHandle"/>
              </div>
              <div class="mb-4">
                <form-label :show="true" for="Handle">
                  Handle
                </form-label>
                <text-field id="Handle" type="text" name="handle" :value="handle" :error="fields.includes('handle')" :readonly="true"/>
              </div>
            </brick>

            <brick>
              <form-label :show="true" for="description">
                Description
              </form-label>
              <markdown-editor name="description" :value="item.description || ''"/>
            </brick>
            <brick>
              <form-label :show="true" for="excerpt">
                Excerpt
              </form-label>
              <markdown-editor name="excerpt" :value="item.excerpt || ''" height="32"/>
            </brick>
            <div class="flex">
              <div class="w-1/2">
                <primary-button type="submit">
                  Update item
                </primary-button>
              </div>
              <div class="w-1/2 text-right">
                <warning-button type="button" initializingText="Hold to delete" :action="deleteItem">
                  Delete item
                </warning-button>
              </div>
            </div>
          </div>
          <div class="w-full lg:w-2/5 px-2">
            <brick>
              <form-label :show="true">
                Tags
              </form-label>
              <tag-select namePrefix="tags" :selectedTags="tags"/>
            </brick>
            <brick>
              <form-label :show="true" :for="`collections`">
                Collections
              </form-label>
              <asset-select namePrefix="collections" asset="collections" :selectedIds="item.collections"/>
            </brick>
          </div>
        </div>
      </form>
    </section>
  </admin-container>
</template>

<script>
import axios from 'axios';
import _ from 'lodash';
import ErrorsBlock from '../snippets/errors-block.vue';
import Brick from '../components/brick.vue';
import PrimaryButton from '../components/primary-button.vue';
import WarningButton from '../components/warning-button.vue';
import AdminContainer from '../snippets/admin-container.vue';
import TextField from '../components/text-field.vue';
import EmailField from '../components/email-field.vue';
import FormLabel from '../components/form-label.vue';
import SelectField from '../components/select-field.vue';
import IconAddItem from '../components/icon-add-item.vue';
import Heading3 from '../components/heading-3.vue';
import AssetSelect from '../components/asset-select.vue';
import TagSelect from '../components/tag-select.vue';
import MarkdownEditor from '../components/markdown-editor.vue';

export default {
  name: 'item',
  components: {
    "errors-block": ErrorsBlock,
    "brick": Brick,
    "primary-button": PrimaryButton,
    "warning-button": WarningButton,
    "admin-container": AdminContainer,
    "text-field": TextField,
    "email-field": EmailField,
    "form-label": FormLabel,
    "select-field": SelectField,
    "icon-add-item": IconAddItem,
    "heading-3": Heading3,
    "markdown-editor": MarkdownEditor,
    "tag-select": TagSelect,
    "asset-select": AssetSelect,
  },
  data () {
    const { item, errors } = window.siteData;
    item.description = _.unescape(item.description);
    return {
      item: item,
      errors, errors,
      fields: errors ? errors.map(error => error.field) : [],
      handle: item.handle || '',
      title: item.title || '',
      tags: item.tags || [],
    }
  },
  methods: {
    createHandle (e) {
      this.handle = this.handleize(e.target.value);
      this.permalink = `/${this.handle}`;
    },
    handleize (str) {
      return str.toLowerCase().replace(/[^\w\u00C0-\u024f]+/g, "-").replace(/^-+|-+$/g, "");
    },
    deleteItem () {
      const url = `/admin/items/${this.item.id}`;
      axios.delete(url).then(res => {
        window.location.href = '/admin/items';
      })
    }
  }
}
</script>
