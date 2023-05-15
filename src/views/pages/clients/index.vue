<script>
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
import Clients from "./clients.vue";
/**
 * Dashboard component
 */
export default {
  components: {
    Layout,
    PageHeader,
    Clients,
  },
  data() {
    return {
      title: "Clients",
      items: [
        {
          text: "Mo9ef",
        },
        {
          text: "Clients",
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
        <Clients />
      </div>
    </div>
  </Layout>
</template>
