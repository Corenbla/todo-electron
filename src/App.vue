<template>
  <div id="app">
    <a @click="$router.go(-1)">back</a>
    <router-view />
  </div>
</template>
