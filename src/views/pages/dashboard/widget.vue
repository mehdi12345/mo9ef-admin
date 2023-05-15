<script>
import firebase from "firebase/app";
import "firebase/firestore";
/**
 * Widget Component
 */
export default {
  data() {
    return {
      statData: [],
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      var db = firebase.firestore();
      db.collection("ads")
        .get()
        .then((querySnapshot) => {
          this.statData.push({
            title: "Nombre d'annonces",
            icon: "ri-file-list-line",
            value: querySnapshot.docs.length,
          });
        });
      db.collection("users")
        .get()
        .then((querySnapshot) => {
          this.statData.push({
            title: "Nombre d'utilisateurs",
            icon: "ri-group-line",
            value: querySnapshot.docs.length,
          });
        });
    },
  },
};
</script>

<template>
  <div class="row">
    <div class="col-md-6" v-for="(data, index) in statData" :key="index">
      <div class="card">
        <div class="card-body">
          <div class="media">
            <div class="media-body overflow-hidden">
              <p class="text-truncate font-size-14 mb-2">{{ data.title }}</p>
              <h4 class="mb-0">{{ data.value }}</h4>
            </div>
            <div class="text-primary">
              <i :class="`${data.icon} font-size-24`"></i>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <!-- end row -->
</template>
