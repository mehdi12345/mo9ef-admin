<script>
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
import Users from "./notifications.vue";
/**
 * Dashboard component
 */
export default {
  components: {
    Layout,
    PageHeader,
    Users,
  },
  data() {
    return {
      title: "Users",
      items: [
        {
          text: "Mo9ef",
        },
        {
          text: "Users",
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
        <Users />
      </div>
    </div>
  </Layout>
</template>
