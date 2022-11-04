<script>
import axios from "axios";
import "vue3-carousel/dist/carousel.css";
import { Carousel, Slide, Pagination, Navigation } from "vue3-carousel";

export default {
  components: {
    Carousel,
    Slide,
    Pagination,
    Navigation,
  },
  data() {
    return {
      title: "",
      screen: "",
      generator: "",
      captions: [],
      assets: [],
      slide: 0,
      sliding: null,
      isLoading: true,
    };
  },
  mounted() {
    axios.get("/screen-schedule-config.xml").then((response) => {
      const parser = new DOMParser();
      const xmlDoc = parser.parseFromString(response.data, "text/xml");

      this.title =
        xmlDoc.getElementsByTagName("title")[0].childNodes[0].nodeValue;
      this.screen =
        xmlDoc.getElementsByTagName("screen")[0].childNodes[0].nodeValue;
      this.generator =
        xmlDoc.getElementsByTagName("generator")[0].childNodes[0].nodeValue;

      const captions = xmlDoc.getElementsByTagName("caption");
      for (const element of captions) {
        this.captions.push(element.childNodes[0].nodeValue.toString());
      }

      const assets = xmlDoc.getElementsByTagName("asset");
      for (const element of assets) {
        this.assets.push(element.attributes[1].value);
      }
      this.isLoading = false;
    });
  },
};
</script>

<template>
  <h1 class="pb-3">{{ title }}</h1>
  <h2>{{ screen }}</h2>
  <h4>{{ generator }}</h4>

  <carousel autoplay="5000" v-if="!isLoading" :wrapAround="true">
    <slide v-for="(caption, index) of captions" :key="index" class="caption">
      <div class="card bg-dark text-white rounded-0">
        <img :src="assets[index]" class="card-img" alt="Stony Beach" />
        <div class="card-img-overlay fade-in">
          <h5 class="card-title">{{ caption }}</h5>
          <p class="card-text">
            {{ caption }}
          </p>
        </div>
      </div>
    </slide>

    <template #addons>
      <navigation />
      <pagination />
    </template>
  </carousel>
</template>

<style lang="scss">
.card-img-overlay {
  text-align: left;
  opacity: 0;
  transition: opacity 0.3s ease-in-out;
  top: auto;
  background: #0000006e;
  cursor: pointer;
}
.caption {
  &:hover {
    .card-img-overlay {
      opacity: 1;
      transition: opacity 0.3s ease-in-out;
    }
  }
}
</style>
