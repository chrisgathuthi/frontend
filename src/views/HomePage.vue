<script setup>
import { userStore } from '@/store/store.js'
import { onMounted, ref } from 'vue';
import { useRouter } from 'vue-router'

const store = userStore()
const router = useRouter()

const mergedPosts = ref([])

const mergePosts = () => {
  if (store.userEntity.subscribers) {
    return store.userEntity.subscribers.forEach(sub => {
      mergedPosts.value.push(...sub.posts)

    });
  }
}
onMounted(async () => {
  await store.fetchUserFromDB()
  mergePosts()
  mergedPosts.value.sort((a, b) => {
    return new Date(a.date) - new Date(b.date)
  })

})
const getSubByPostId = (id) => {
  return store.userEntity.subscribers.find(sub => sub.profile.id === id)
}
</script>

<template>
  <ion-page>
    <ion-content :fullscreen="true" class="ion-padding ion-margin">
      <ion-row>
        <ion-col>
          <ion-card v-for="post in mergedPosts" :key="post.id" class="ion-no-margin ion-margin-vertical">
            <ion-card-header @click="router.push({ name: 'Profile', params: { id: post.userId } })">
              <ion-card-subtitle>
                {{ new Date(post.date).toDateString() }}
              </ion-card-subtitle>
              <ion-card-title>
                {{ getSubByPostId(post.userId).profile.name }}

              </ion-card-title>
            </ion-card-header>
            <ion-card-content>
              <ion-img :src="post.photo" class="ion-margin-bottom">

              </ion-img>
            </ion-card-content>

          </ion-card>
        </ion-col>
      </ion-row>

    </ion-content>

  </ion-page>
</template>
