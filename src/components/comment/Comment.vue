<template>
  <article class="media">
  <figure class="media-left">
    <p class="image is-64x64">
      <img src="https://bulma.io/images/placeholders/128x128.png">
    </p>
  </figure>
  <div class="media-content">
    <div class="content">
      <p>
        <strong>John Smith</strong> <small>{{comment.user}}</small> <small>31m</small>
        <br>
        {{comment.body}}
      </p>
    </div>
  </div>
</article>
</template>

<script>
export default {
    name: "comment",
    props: ["comment"]
}
</script>

<style lang="scss" >
@import "../../scss/variables";
@import "../../scss/bulma";

.container {
  @include flex-direction(column);
}

.media-content {
  width: 100%;
}

p {
  width: 100%;
  overflow-wrap: break-word;
}
</style>