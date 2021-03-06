<template>
  <Page class="page">
    <ActionBar class="action-bar">
      <NavigationButton
        ios:visibility="collapsed"
        icon="res://menu"
        @tap="onDrawerButtonTap"
      />
      <ActionItem
        icon="res://menu"
        android:visibility="collapsed"
        @tap="onDrawerButtonTap"
        ios.position="left"
      />
      <Label class="action-bar-title" text="Home" />
    </ActionBar>
    <GridLayout rows="*" class="page">
      <RadListView
        ref="postList"
        for="item in posts"
        @itemTap="onItemTap"
        :pullToRefresh="true"
        @pullToRefreshInitiated="onPullToRefreshInitiated"
        loadOnDemandMode="Auto"
        @loadMoreDataRequested="onLoadMoreDataRequested"
        loadOnDemandBufferSize="2"
      >
        <v-template>
          <StackLayout class="item" orientation="vertical">
            <Label :text="item.title" class="post-title"></Label>
            <Label :text="item.body" class="post-body"></Label>
          </StackLayout>
        </v-template>
      </RadListView>
      <ActivityIndicator
        :busy="loadingPost"
        :visibility="loadingPost ? 'visible' : 'collapse'"
      />
    </GridLayout>
  </Page>
</template>

<script>
import * as utils from "~/shared/utils";
import SelectedPageService from "../shared/selected-page-service";
import PostDetail from "./PostDetail";

export default {
  data() {
    return {
      posts: [],
      currentPage: 1,
      loadingPost: true,
      noMorePost: false,
    };
  },
  mounted() {
    SelectedPageService.getInstance().updateSelectedPage("Home");
  },
  created() {
    this.loadPost();
  },
  methods: {
    async loadPost(page = 1) {
      this.loadingPost = true;
      await fetch(`http://10.0.2.2:3000/posts?_page=${page}&_limit=20`)
        .then((response) => response.json())
        .then((r) => {
          r.forEach((post) => {
            this.posts.push(post);
          });
          this.loadingPost = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    onDrawerButtonTap() {
      utils.showDrawer();
    },
    onItemTap({ item }) {
      this.$navigateTo(PostDetail, {
        props: {
          post: item,
        },
      });
    },
    onBusyChanged(data) {
      console.log(data);
    },
    onPullToRefreshInitiated({ object }) {
      this.$nextTick(() => {
        this.loadPost();
        object.notifyPullToRefreshFinished();
      });
    },
    onLoadMoreDataRequested() {
      this.currentPage++;
      this.loadPost(this.currentPage);
      this.$refs.postList.nativeView.notifyLoadOnDemandFinished();
    },
  },
};
</script>

<style scoped lang="scss">
// Start custom common variables
@import "~@nativescript/theme/scss/variables/blue";
// End custom common variables

.item {
  border-bottom-color: black;
  border-bottom-style: solid;
  border-bottom-width: 1;
}

.post-title {
  font-size: 18;
}
</style>
