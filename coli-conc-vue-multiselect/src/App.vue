<template>
  <div id="app">
    <div>
      <!-- Just a test commit -->
      <!-- Configure Multiselect component -->
      <multiselect
        v-model="selected"
        track-by="uri"
        placeholder="Type to search"
        open-direction="bottom"
        :customLabel="labelFor"
        :options="results"
        :multiple="true"
        :loading="isLoading"
        :internal-search="false"
        :clear-on-select="false"
        :close-on-select="false"
        :max-height="300"
        :show-no-results="false"
        :hide-selected="true"
        :preserveSearch="true"
        @search-change="search"
      >
        <!-- Customize tags via Slot -->
        <template slot="tag" slot-scope="{ option, remove }">
          <span class="multiselect__tag">
            <span>
              {{ labelFor(option) }}
            </span>
            <i
              aria-hidden="true"
              tabindex="1"
              class="multiselect__tag-icon"
              @click="remove(option)"
            />
          </span>
        </template>
      </multiselect>
    </div>
  </div>
</template>

<script>
import Multiselect from "vue-multiselect";
import cdk from "cocoda-sdk";
import jskos from "jskos-tools";

export default {
  name: "App",
  components: {
    Multiselect,
  },
  data() {
    return {
      isLoading: false,
      // The selected concepts (= shown as tags)
      selected: [],
      // The results of the search query
      results: [],
      // The registry we are using for API queries
      registry: cdk.initializeRegistry({
        provider: "ConceptApi",
        api: "https://coli-conc.gbv.de/api/",
      }),
      // The scheme we want to tag with
      scheme: { uri: "https://www.ixtheo.de/classification/" },
    };
  },
  methods: {
    // We want to show concepts as [notation] [label]
    labelFor(concept) {
      return `${jskos.notation(concept)} ${jskos.prefLabel(concept)}`;
    },
    async search(query) {
      // TODO: Previous query can override results of new query.
      this.isLoading = true;
      let results = [];
      if (query) {
        results = await this.registry.search({
          search: query,
          scheme: this.scheme,
        });
      }
      this.results = results;
      this.isLoading = false;
    },
  },
};
</script>

// Styles for Multiselect component
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
