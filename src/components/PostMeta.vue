<template>
  <div class="post-meta">
    Publicado em {{ formattedDate }}
  </div>
</template>

<script>
export default {
  props: ["post"],
  computed: {
    formattedDate() {
      return this.formatDate(new Date(this.post.date));
    }
  },
  methods: {
    formatDate (date) {
      var date = new Date(date),
        day = "" + date.getDate(),
        month = "" + (date.getMonth() + 1),
        year = date.getFullYear();

      if (month.length < 2) month = "0" + month;
      if (day.length < 2) day = "0" + day;

      return [day, month, year].join("/");
    },
  },
};
</script>

<style lang="scss">
.post-meta {
  font-size: 0.8em;
  opacity: 0.8;
}
</style>