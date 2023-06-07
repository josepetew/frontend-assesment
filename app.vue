<template>
  <div class="container">
    <h4>initialImageIndex=0</h4>
    <Slider :imagesUrls="imagesUrls" :initialImageIndex="0" />
    <h4>initialImageIndex=2</h4>
    <Slider :imagesUrls="imagesUrls" :initialImageIndex="2" />
    <aside>Drag to change image</aside>
    <h1>Assignment solution</h1>
    <p>Assignment was implemented using Nuxt 3.</p>
    <p>
      Repo available at
      <a href="https://github.com/josepetew/frontend-assesment" target="_blank"
        >https://github.com/josepetew/frontend-assesment</a
      >
    </p>
    <p>
      Solution is responsive, there is no resize event handlers implemented yet
      so please refresh the page to text on smaller screens. It also works on
      mobile devices.
    </p>
    <p>
      Original assignment can be found at:
      <a href="http://challenge.publitas.com/frontend.html" target="_blank"
        >http://challenge.publitas.com/frontend.html</a
      >
    </p>
    <h3>Notes</h3>
    <ul>
      <li>
        In the original assignment I found that it doesnt work on mobile
        devices. perhaps a feature not a bug ;). But in case it was required
        that could be a potential bug to consider that was fixed in this
        solution.
      </li>
      <li>
        For the purpose of this assignment, I am commiting '.output' and 'dist'
        directories created when you run 'npm run generate', keep in mind that
        this is not recommended but it was done to satisfy the requirement of
        having the the build dist directory present in the solution repo.
      </li>
    </ul>

    <p>
      Hopefuly the solution satisfy the expectations. Happy to discuss it in
      detail.
    </p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      imagesUrls: ["/img/0.jpg", "/img/1.jpg", "/img/2.jpg", "/img/3.jpg"],
    };
  },
};
</script>

<style scoped>
* {
  font-family: "Roboto", Arial, sans-serif;
  color: #333;
}

.container {
  height: 400px;
  max-width: 640px;
  margin-right: auto;
  margin-left: auto;
}

aside {
  margin-top: 12px;
  text-align: center;
  color: #999;
  font-size: 12px;
}
</style>
