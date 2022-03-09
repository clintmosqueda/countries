<script>
export default {
  
}
</script>

<template>
  <div class="card">
    <figure class="card-image">
      <slot name="image"></slot>
    </figure>
    <div class="card-info">
      <h2 class="card-country"><slot name="country"></slot></h2>
      <p class="card-population">
        <strong>Population:</strong> <span><slot name="population"></slot></span>
      </p>
      <p class="card-region">
        <strong>Region:</strong> <span><slot name="region"></slot></span>
      </p>
      <p class="card-capital">
        <strong>Capital:</strong> <span><slot name="capital"></slot></span>
      </p>
    </div>
  </div>
</template>

<style lang="scss">
@import '../assets/base.css';

.card {
  background: var(--color-background);
  box-shadow: 0px 20px 40px 0px rgba(140, 149, 157, 0.5);

  &-info {
    padding: 10px;
  }

  &-image {
    max-width: 270px;
    width: 100%;
    
    img {
      width: 100%;
      display: block;
    }
  }

  &-country {
    font-weight: 600;
  }
}
</style>