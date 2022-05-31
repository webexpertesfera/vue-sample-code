<template>
  <div class="bg-base">
    <sticky
      ref="stickyTab"
      :sticky-top="55"
      :background="'#ffffff'"
      :z-index="100"
      style="overflow: hidden !important;"
    >
      <v-toolbar style="overflow: hidden !important;">
        <v-btn
          icon
          :background="'#ffffff'"
          @click="$router.push(`/category/${premId}`)"
        >
          <v-icon>mdi-arrow-left</v-icon>
        </v-btn>
        <v-toolbar-title
          class="flex text-center bold"
          style="margin-left: -50px"
        >
          {{ serviceMethod == 'pickup'?'Pick-Up':'Dine-In' }} Menu
        </v-toolbar-title>
      </v-toolbar>
    </sticky>
    <v-parallax
      height="300"
      :src="backgroundImage"
      class="venue-hero"
    >
      <v-container>
        <v-row>
          <v-col>
            <h1
              class="font-weight-regular"
            >
              {{ venueName }}
            </h1>
            <p>
              {{ venueDistance }} km
            </p>
          </v-col>
        </v-row>
      </v-container>
    </v-parallax>
    <div
      class="white"
    >
      <v-container>
        <v-row>
          <v-col v-if="serviceMethod === 'table_service'">
            <h4>Dine-In</h4>
            <p>
              Table <strong>#{{ tableNumber }}</strong>
              <v-btn
                text
                color="accent"
                href="#"
                plain
                small
                @click="showDialog = true"
              >
                Change Table No.?
              </v-btn>
              <v-btn
                text
                color="accent"
                href="#"
                plain
                small
                @click="changeServiceMethod()"
              >
                Change to Pickup?
              </v-btn>
            </p>
          </v-col>
          <v-col v-if="serviceMethod === 'pickup'">
            <h4>Pick Up</h4>
            <p>
              {{ date }}, {{ time }}
              <v-btn
                text
                color="accent"
                href="#"
                plain
                small
                @click="showDialog = true"
              >
                Change Date/Time?
              </v-btn>
              <v-btn
                text
                color="accent"
                href="#"
                plain
                small
                @click="changeServiceMethod()"
              >
                Change to Dine-In?
              </v-btn>
            </p>
          </v-col>
        </v-row>
      </v-container>
    </div>
    <sticky
      ref="stickyTab"
      :sticky-top="120"
      :background="'#ffffff'"
      :z-index="4"
      style="overflow: hidden !important;"
    >
      <v-container class="pb-0">
        <scrollactive
          class="menuNav overflow-hidden"
          style="overflow: hidden !important;"
          :offset="130"
        >
          <v-tabs
            center-active
            show-arrows
            class="menuNav"
            color="accent"
          >
            <!--.filter((n, i) => filterCategories(i) === true)-->
            <v-tab
              v-for="(item, id) in category"
              :key="id"
              :href="'#' + item.name.split(' ').join('')"
              class="scrollactive-item"
            >
              <!-- {{ category.filter((n, i) => filterCategories(i) === true)}} -->
              {{ item.name }}
            </v-tab>
          </v-tabs>
        </scrollactive>
      </v-container>
    </sticky>
    <v-container class="px-0">
      <div
        v-for="(categ, id) in category"
        :key="id"
        :ref="categ.name"
        class="mb-10 category-box"
      >
        <div 
          :id="categ.name.split(' ').join('')" 
          class="category-scroller" 
        />
        <h2
          class="mt-5 mb-1 font-weight-regular ml-3"
        >
          <strong>{{ categ.name }}</strong>
        </h2>
        <v-row
          v-for="(menuItem, idx) in filterMenuItems(categ)"
          :key="idx"
          dense
        >
          <v-col
            v-for="(item, i) in menuItem"
            :key="i"
            cols="12"
            md="6"
          >
            <router-link
              :to="{
                name: 'ItemOptions',
                params: {
                  item_id: item._id,
                  name: item.name,
                  item_group_id: 2,
                },
              }"
            >
              <v-card
                tile
                elevation="0"
                @click.stop="showMenu(menuItem, i)"
              >
                <v-list-item
                  three-line
                >
                  <v-list-item-content>
                    <v-list-item-title>
                      <strong>{{ item.name }}</strong>
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      {{ item.description }}
                    </v-list-item-subtitle>
                    <v-list-item-title>
                      ${{
                        (item.price / 100).toFixed(
                          2
                        )
                      }}
                    </v-list-item-title>
                  </v-list-item-content>
                  <v-list-item-avatar
                    tile
                    size="80"
                  >
                    <img
                      v-if="item.image_url"
                      :src="item.image_url"
                      :alt="item.name"
                    >
                  </v-list-item-avatar>
                </v-list-item>
              </v-card>
            </router-link>
          </v-col>
        </v-row>
        <v-row
          dense
        >
          <v-col
            v-for="(items, idx) in filteredItemGroups(categ.name)"
            :key="idx * 150 + 150"
            cols="12"
            md="6"
          >
            <v-card
              tile
              elevation="0"
              @click="setItemGroupData(categ, items._id)"
            >
              <router-link
                :to="{
                  name: 'ItemGroupList',
                  params: {
                    itemGroupId: items._id,
                    category: categ.name,
                  },
                }"
              >
                <v-list-item
                  three-line
                >
                  <v-list-item-content>
                    <v-list-item-title>
                      <strong>{{ items.name }}</strong>
                    </v-list-item-title>
                    <v-list-item-subtitle>
                      {{ items.description }}
                    </v-list-item-subtitle>
                    <v-list-item-subtitle>
                      <strong>{{ setItemGroupData(categ, items._id) }} variations</strong>
                    </v-list-item-subtitle>
                  </v-list-item-content>
                  <v-list-item-avatar
                    tile
                    color="grey"
                    size="80"
                  >
                    <img
                      :src="items.image_url"
                      :alt="items.name"
                    >
                  </v-list-item-avatar>
                </v-list-item>
              </router-link>
            </v-card>
          </v-col>
        </v-row>
      </div>
      <v-dialog
        v-model="showDialog"
        persistent
        max-width="400"
        overlay-color="primary"
        overlay-opacity="0.6"
      >
        <v-card class="py-5">
          <div v-if="serviceMethod == 'table_service'">
            <v-card-title>Dine-In Table Number?</v-card-title>
            <v-card-text>
              <v-text-field
                v-model="tableNumber"
                outlined
                persistent-hint
                label="Enter table number here"
                hint="Enter the table number displayed at your table"
                class="mb-3"
                :rules="[rules.required]"
              />
            </v-card-text>
          </div>
          <div v-else-if="serviceMethod == 'pickup'">
            <v-card-title>Select pick up time</v-card-title>
            <v-card-text>
              <v-select
                v-model="date"
                :items="dates"
                label="Select Day"
                outlined
              />
              <v-select
                v-model="time"
                :items="times"
                label="Select Time"
                outlined
              />
            </v-card-text>
          </div>
          <v-card-text>
            <v-btn
              color="accent"
              block
              @click="serviceMethod == 'table_service' ? setTableNo() : setPickUpTime()"
            >
              Save
            </v-btn>
          </v-card-text>
        </v-card>
      </v-dialog>
    </v-container>
  </div>
</template>
<style>
  .category-box {
    position: relative;
  }
  .category-scroller {
      position: absolute;
      top: -100px;
  }
</style>
<script>
import { venueData } from "@/data/venues";
import getSinglePremise from "../api/singlePremise";
import getMenu from "../api/menu";

export default {
	name: "DineIn",
	components: {
		//StickyElement
	},
	props: {
		premiseId: {
			default: "2",
			type: String,
		},
    venueDistance: {
      default: "1.2",
      type: String
    }
	},
	data() {
		return {
			venueName: "Name",
			menuDialog: false,
			menuItemName: "",
			menuItemDescription: "",
			menuOptions: "",
			menuItemPrice: 0,
			menuImageUrl: "",
			itemAmount: 1,
			itemTotal: 0,
			tableNumber: "",
			backgroundImage: require("@/assets/images/venue_image.jpg"),
			venue: venueData,
			category: "",
			menuItems: [],
			date: "Today",
			checkedBoxes: [],
			itemMin: 0,
			itemMax: 0,
			radioButtons: null,
			itemGroups: [],
      serviceMethod: null,
      showDialog: false,
      dates: [
        { text: 'Today' }
      ],
      time: "9:00am - 9:15am",
      rules: {
        required: value => !!value || 'Required.'
      },
      times: ['As soon as possible'],
      premId: null
		};
	},
	async created() {
		const responseWithName = await getSinglePremise(
			this.$route.params.premiseId
		);
    this.premId = this.$route.params.premiseId

		this.venueName = responseWithName.data[0].name
		this.backgroundImage = responseWithName.data[0].default_activity_img

		const response = await getMenu(this.$route.params.premiseId);
		const menuData = await response.items;

    if(response.pickup_details !== undefined){
      const timeGap = response.pickup_details.period_seconds
      const lastTime = response.pickup_details.max_seconds

      let timeWait = timeGap;

      while(timeWait <= lastTime){
        this.times.push(`Approx ${timeWait / 60} minutes`)
        timeWait += timeGap;
      }
    }

    this.time = this.$store.getters.getPickupTime;

		this.category = response.categories;

		this.$store.dispatch("setItemGroups", response.item_groups);

		this.itemGroups = response?.item_groups ?? null;

		const menu = [];

		for (let i = 0; i < menuData.length; i++) {
			const menuItemsFromCat = [];
			for (let j = 0; j < menuData[i].items.length; j++) {
          menuItemsFromCat.push(menuData[i].items[j]);
			}
			  menu.push(menuItemsFromCat);
		}
		this.menuItems = menu //.filter(n => n.length > 0);
    this.serviceMethod = this.$store.getters.getServiceMethod;
    console.log(this.$store.state);

    let premise = window.localStorage.getItem('premise');
    let tableNo = window.localStorage.getItem('tableNo');

    let showDialog = true;

    if(premise && tableNo){
      if(premise === this.premId){
        this.tableNumber = tableNo;
        this.$store.dispatch("setTableNo", this.tableNumber)
        showDialog = false;
      }
    }
    this.showDialog = showDialog;

	},
	methods: {
		showMenu: function (items, n) {
			this.$store.dispatch("setMenuItemName", items[n].name);
			this.$store.dispatch(
				"setMenuItemDescription",
				items[n].description
			);
			this.$store.dispatch("setMenuItemPrice", items[n].price);
			this.$store.dispatch("setMenuItemUrl", items[n].image_url);
			this.$store.dispatch(
				"setMenuOptions",
				items[n].selections.filter((n) => n.is_active === true)
			);
			this.$store.dispatch("setMenuItemUrl", items[n].image_url);
			this.menuOptions = items[n].selections.filter(
				(n) => n.is_active === true
			);
			this.$store.dispatch(
				"setItemMin",
				this.menuOptions.map(n => n.min)
			);
			this.$store.dispatch(
				"setItemMax",
				this.menuOptions.map(n => n.max)
			);
		},
		setItemGroupData(categ, itemGroupId) {
			const itemsWithGroupId = [];

			for (let i = 0; i < this.menuItems.length; i++) {
				for (let j = 0; j < this.menuItems[i].length; j++) {
					if (
						this.menuItems[i][j].category === categ.name
            &&
						this.menuItems[i][j].item_group_id.includes(itemGroupId)
					) {
						itemsWithGroupId.push(this.menuItems[i][j]);
					}
				}
			}
      this.$store.dispatch("setGroupedItems", itemsWithGroupId);
      return itemsWithGroupId.length
		},
		filterMenuItems(categ) {
			const filteredMenuItems = this.menuItems.map((n) => n.filter((m) => m.category === categ.name))
				.filter((o) =>o.length > 0);

			const menuUndefined = filteredMenuItems[0]
				.map((n) => n.item_group_id)
				.includes(undefined);

			return menuUndefined
				? filteredMenuItems.map((n) => n.filter((n) => {
          if(this.serviceMethod === 'pickup'){
            return n.status === "active" && n.service_method !== 1
          } else {
            return  n.status === "active" && n.service_method !== 2
          }
        }))
				: filteredMenuItems.map((p) =>
						p.filter((q) => {
              if(this.serviceMethod === 'pickup'){
                return q.item_group_id.length === 0 && q.status === "active" && q.service_method !== 1
              } else {
                return q.item_group_id.length === 0 && q.status === "active" && q.service_method !== 2
              }
            })
        )
		},
		filteredItemGroups(categ) {
			if (!this.itemGroups) return;

			const active = this.itemGroups.filter((n) => n.is_active === true);

			const itemsWithGroupIds = this.menuItems
				.map((n) => n.filter((m) => m.category === categ))
				.filter((o) => o.length > 0)
				.map((p) => p.filter((q) => q.item_group_id.length > 0));

			const render = itemsWithGroupIds.map((n) =>
				n.map((m) => m.item_group_id[0])
			);

			const dataToRender = active.filter((n, i) => {
        if(render[i] !== undefined){
          return (render[i]).includes(n._id)
      }
        return render[i]
      });

			return dataToRender;
		},
    filterCategories(){
      return this.category
    },
    changeServiceMethod(){
      if(this.serviceMethod === 'pickup'){
        this.$store.dispatch('setServiceMethod', 'table_service')
        this.serviceMethod = 'table_service'
      } else {
         this.$store.dispatch('setServiceMethod', 'pickup')
        this.serviceMethod = 'pickup'
      }
    },
      setTableNo(){
      if(this.tableNumber !== ""){
        this.showDialog = false
        this.$store.dispatch("setTableNo", this.tableNumber)
        window.localStorage.setItem('tableNo', this.tablenumber);
      }
    },
      setPickUpTime(){
        this.showDialog = !this.showDialog
        this.$store.dispatch("setPickupTime", this.time)
      }
	},

};
</script>
