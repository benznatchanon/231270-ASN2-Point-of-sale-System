<template>
  <div id="app">
    <b-navbar class="is-primary">
      <template #brand>
        <b-navbar-item tag="router-link" :to="{ name: 'Home' }">
          BECPoSS
        </b-navbar-item>
      </template>

      <template #end>
        <b-navbar-item tag="router-link" :to="{ name: 'Scan' }">
          Scan
        </b-navbar-item>
        <b-navbar-item tag="router-link" :to="{ name: 'Basket' }">
          Basket
        </b-navbar-item>
      </template>
    </b-navbar>

    <router-view />
  </div>
</template>
