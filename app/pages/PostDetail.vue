<template>
  <Page class="page">
    <ActionBar class="action-bar">
      <NavigationButton
        ios:visibility="collapsed"
        icon.decode="font://&#xf060;"
        class="fas action-icon"
        @tap="$navigateBack({}, null)"
      />
      <ActionItem
        icon.decode="font://&#xf060;"
        class="fas action-icon"
        android:visibility="collapsed"
        ios.position="left"
        @tap="$navigateBack({}, null)"
      />
      <Label class="action-bar-title" :text="post.title" />
    </ActionBar>
    <GridLayout rows="*" class="page">
      <Label :text="post.title" />
    </GridLayout>
  </Page>
</template>

<script>
export default {
  props: ["post"],
  mounted() {
    console.log(this.post);
  },
};
</script>

<style scoped lang="scss">
// Start custom common variables
@import "~@nativescript/theme/scss/variables/blue";

.action-icon {
  font-size: 20;
}
</style