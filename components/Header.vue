<template>
  <a-layout-header>
    <a-menu theme="dark" mode="horizontal" :style="{ lineHeight: '64px' }">
      <a-menu-item key="1">
        <nuxt-link to="/">Home</nuxt-link>
      </a-menu-item>
      <a-menu-item key="2">
        <nuxt-link to="/dogs">Browse Dogs</nuxt-link>
      </a-menu-item>
      <a-menu-item key="3">
        <nuxt-link to="/cats">Browse Cats</nuxt-link>
      </a-menu-item>
    </a-menu>
  </a-layout-header>
</template>
