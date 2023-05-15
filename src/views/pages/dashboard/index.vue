<script>
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
import Stat from "./widget";
import Annonces from "./annonces.vue";
/**
 * Dashboard component
 */
export default {
  components: {
    Layout,
    PageHeader,
    Stat,
    Annonces,
  },
  data() {
    return {
      title: "Dashboard",
      items: [
        {
          text: "Mo9ef",
        },
        {
          text: "Dashboard",
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
        <Stat />
        <Annonces />
      </div>
    </div>
  </Layout>
</template>
