<template xmlns="http://schemas.nativescript.org/tns.xsd">
  <Page class="page">
    <ActionBar android.systemIcon="ic_menu_back" class="action-bar" :title="appTitle">
      <NavigationButton
          v-if="$isAndroid"
          text="Menu"
          icon="res://ic_menu_white_24dp"
          @tap="$refs.drawer.nativeView.showDrawer()"></NavigationButton>
      <ActionItem
          v-else
          text="Menu"
          ios.position="left"
          icon="res://ic_menu"
          @tap="$refs.drawer.nativeView.showDrawer()"></ActionItem>
    </ActionBar>

    <RadSideDrawer id="drawer" ref="drawer" showOverNavigation="true">
      <StackLayout class="drawer-content" ~drawerContent>
        <StackLayout class="sidedrawer-header"></StackLayout>
        <ListView for="item in topNavItems">
          <v-template>
            <StackLayout @tap="goToPage(item.page)" orientation="horizontal" class="sidedrawer-list-group">
              <Label class="mdi" >{{fonticon(item.icon)}}</Label>
              <Label :text="item.text"></Label>
            </StackLayout>
          </v-template>
        </ListView>
      </StackLayout>
      <StackLayout ~mainContent>
        <router-view/>
      </StackLayout>
    </RadSideDrawer>

  </Page>
</template>
<script>
  import {fonticon} from 'nativescript-fonticon'
  export default {
    data() {return {
      topNavItems: [
        {icon: 'mdi-home', text: 'Home', page: 'home'},
        {icon: 'mdi-date-range', text: 'Events', page: 'events'},
        {icon: 'mdi-nature-people', text: 'Conferences', page: 'conferences'}
      ]
    }},
    computed: {
      appTitle() {
        return this.$store.state.route &&
          this.$store.state.route.meta &&
          this.$store.state.route.meta.title ||
            'HasGeek'

      }
    },
    methods: {
      fonticon(icName) {
        try {return fonticon(icName)}
        catch (e) {return ''}
      },
      goToPage(page) {
        this.$router.push(page)
        this.$refs.drawer.nativeView.closeDrawer()
      }
    },
    mounted () {
      // Add shadow to iOS Drawer
      let _drawer = this.$refs.drawer
      if(_drawer._nativeView.ios)  {
        const ios = _drawer._nativeView.ios
        // .. but if the menu is drawn 'above' the hostview, do this:
        ios.defaultSideDrawer.style.shadowMode = 2; // TKSideDrawerShadowMode.SideDrawer;
        // if you have shadowMode = 2, then you can add a little dim to the lower layer to add some depth. Keep it subtle though:
        ios.defaultSideDrawer.style.dimOpacity = 0.12;

        // then tweak the shadow to your liking:
        ios.defaultSideDrawer.style.shadowOpacity = 0.75; // 0-1, higher is darker
        ios.defaultSideDrawer.style.shadowRadius = 5; // higher is more spread

      }
    }

  }
</script>
<style lang="scss">
  @import "styles/hasgeek";
  .drawer-content {
    background-color: white;
  }
  #drawer > StackLayout {
    height: 100%;
  }
  .sidedrawer-header {
    background-color: $hg-orange;
  }
  .sidedrawer-list-group {
    padding: 10;
  }
  .sidedrawer-list-group .mdi {
    padding-right: 20;
  }
</style>