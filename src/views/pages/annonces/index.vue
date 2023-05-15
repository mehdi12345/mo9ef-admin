<script>
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
import Annonces from "./annonces.vue";
/**
 * Dashboard component
 */
export default {
  components: {
    Layout,
    PageHeader,
    Annonces,
  },
  data() {
    return {
      title: "Annonces",
      items: [
        {
          text: "Mo9ef",
        },
        {
          text: "Annonces",
          active: true,
        },
      ],
    };
  },
};
</script>

<template>
  <Layout>
    <PageHeader :title="title" :items="items" />
    <div class="row">
      <div class="col-xl-12">
        <Annonces />
      </div>
    </div>
  </Layout>
</template>
