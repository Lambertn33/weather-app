<template>
  <div>
    <Suspense>
      <template #default>
        <CityViewData />
      </template>
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script>
import CityViewData from '../components/CityViewData.vue'
import CityViewSkeleton from '../components/CityViewSkeleton.vue'
export default {
  components: { CityViewData, CityViewSkeleton }
}
</script>

<style>
</style>