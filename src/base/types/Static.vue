<template>
  <div class="text-center">
    <div class="element-type">
      <h3 class="pb-4 mb-4">{{ $formatText(props.name, " / ") }}</h3>
      <form>
        <div>
          <subType :name="props.name" @addStatic="addStatic" ref="viewSaved">
            <slot />
          </subType>
        </div>
      </form>
    </div>
    <!-- get access to the imported static component -->
    <component :is="component" :section="props" v-show="false" ref="importedComponent" />
  </div>
</template>

<script>
import subType from "../subType.vue";
import { importComp } from "../helpers";

export default {
  components: {
    subType,
  },
  props: {
    props: {
      type: Object,
      default: () => {},
    },
    savedView: {
      type: Object,
      default: () => {},
    },
  },
  data() {
    return {
      myHtml: "",
      elements: [],
      imported: false,
    };
  },
  computed: {
    component() {
      const path = `/views/${this.props.name}_${this.props.type}`;
      return importComp(path);
    },
    id() {
      if (this.savedView.id) {
        return this.savedView.id;
      }
      return "id-" + Date.now();
    },
    weight() {
      if (this.savedView.weight === 0 || this.savedView.weight) {
        return this.savedView.weight;
      }
      return "null";
    },
  },

  mounted() {
    if (this.savedView && this.savedView.settings) {
      setTimeout(() => {
        this.$refs.viewSaved.$refs[
          this.props.name
        ].settings = this.savedView.settings;
      }, 500);
    }
    setTimeout(() => {
      this.elements = this.$refs.importedComponent.fields;
    }, 10);
  },
  methods: {
    addStatic(settings) {
      this.$emit("addSectionType", {
        name: this.props.name,
        type: "static",
        settings,
        id: this.id,
        weight: this.weight,
      });
    },
  },
};
</script>

<style lang="scss">
.dashboard {
  button {
    background: black;
    margin: 10px;
    width: auto;
    height: auto;
    max-height: auto;
    padding: 5px;
    min-width: 0;
    max-width: 1000px;
  }
}
</style>
