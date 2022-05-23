<template>
  <div>
    <h1>Сторінка посту з ID {{ $route.params.id }}</h1>
    <div>
      <strong>Назва:</strong> {{ $route.params.title }}
    </div>
    <div>
      <strong>Опис:</strong> {{ $route.params.body }}
    </div>
  </div>
</template>

<script>
export default {};
</script>

<style scoped></style>
