<template>
  <a-input-search
    v-model:value="searchParams.text"
    placeholder="input search text"
    enter-button="Search"
    size="large"
    @search="onSearch"
  />
  <MyDivider />
  <a-tabs v-model:activeKey="activeKey" @change="onTabChange">
    <a-tab-pane key="post" tab="文章">
      <PostList :post-list="postList" />
    </a-tab-pane>
    <a-tab-pane key="picture" tab="图片" force-render>
      <PictureList />
    </a-tab-pane>
    <a-tab-pane key="user" tab="用户">
      <UserList :user-list="userList" />
    </a-tab-pane>
  </a-tabs>
</template>

<script setup lang="ts">
import { ref, watch, watchEffect } from "vue";
import PictureList from "@/components/PictureList.vue";
import PostList from "@/components/PostList.vue";
import UserList from "@/components/UserList.vue";
import MyDivider from "@/components/MyDivider.vue";
import { useRoute, useRouter } from "vue-router";
import myAxios from "@/plugins/myAxios";

const postList = ref([]);

myAxios.post("post/list/page/vo", {}).then((res) => {
  postList.value = res.records;
});

const userList = ref([]);

myAxios.post("user/list/page/vo", {}).then((res) => {
  userList.value = res.records;
});

const router = useRouter();
const route = useRoute();
const activeKey = route.params.category;

const initSearchParams = {
  text: "",
  pageSize: 10,
  pageNum: 1,
};

const searchParams = ref(initSearchParams);

watch(
  searchParams,
  (newSearchParams) => {
    router.push({
      query: newSearchParams,
    });
  },
  {
    deep: true,
  }
);
watchEffect(() => {
  searchParams.value = {
    ...initSearchParams,
    text: route.query.text,
  } as any;
});
const onSearch = (value: string) => {
  alert(value);
  router.push({
    query: {
      text: value,
    },
  });
};

const onTabChange = (key: string) => {
  router.push({
    path: `/${key}`,
    query: searchParams.value,
  });
};
</script>
