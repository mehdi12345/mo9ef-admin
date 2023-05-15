<script>
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
import Categories from "./categories.vue";
/**
 * Dashboard component
 */
export default {
  components: {
    Layout,
    PageHeader,
    Categories,
  },
  data() {
    return {
      title: "Categories",
      items: [
        {
          text: "Mo9ef",
        },
        {
          text: "Categories",
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
        <Categories />
      </div>
    </div>
  </Layout>
</template>
