<template>
    <div class="card">
        <div class="card-body">
            <slot></slot>
        </div>
    </div>
</template>

<style lang="scss" scoped>
.card{
  border: 0;
  border-radius: var(--radius);
  overflow: hidden;
    background: var(--bg-card);

  &-body{
      padding: 20px;
  }

  &-thumbnail{
    border-radius: var(--radius) var(--radius) 0 0;
    overflow: hidden;
    img{
      transition: .3s ease;
    }
    &:hover img{
      transform: scale(1.1)rotate(2deg);
    }
  }
}

</style>