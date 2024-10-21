<template>
  <span class="pt-15">
    <style>
      .text-color {
        color: #8a8a8a;
      }
    </style>
    <v-row>
      <v-col v-for="(item, index) in cards" :key="index">
        <AssetsCardSmall
          :key="index"
          :color="item.color"
          :bgColor="item.bgColor"
          :label="item.label"
          :value="item.value"
          :sub_value="item.sub_value"
        />
      </v-col>
    </v-row>

    <v-row>
      <v-col cols="8">
        <v-card
          height="125"
          elevation="0"
          :style="`background-color:#f2f6f5; border-radius: 12px`"
          class="pt-1"
        >
          <small class="mx-2 text-color" style="font-size: 11px"
            >Overall Booking</small
          >
          <DonutSmall
            :key="keyTabAll"
            name="margin"
            size="100%"
            :total="'100'"
            width="100%"
            :colors="[`#91d23d`, `#3a5824`]"
            :labels="[
              {
                color: `#91d23d`,
                text: `Sold`,
                value: reservedWithoutAdvance.length,
              },
              {
                color: `#3a5824`,
                text: `Vacant`,
                value: availableRooms.length,
              },
            ]"
          />
        </v-card>
      </v-col>
      <v-col cols="4">
        <v-card
          elevation="0"
          dark
          class="text-center"
          :style="`background-color:#d9534f; border-radius: 12px`"
        >
          <div>
            <small class="px-1" style="font-size: 9px">Income</small>
            <div class="pa-1" style="font-size: 14px">
              {{ $utils.currency_format(1500) }}
            </div>
          </div>
          <div style="padding-top: 16px">
            <small class="px-1" style="font-size: 9px">Expenses</small>
            <div class="pa-1" style="font-size: 14px">
              {{ $utils.currency_format(7500) }}
            </div>
          </div>
        </v-card>
      </v-col>
    </v-row>
    <v-row class="pt-3">
      <v-col cols="8" class="pt-2">
        <WidgetsTenDaysForCast :key="keyTabAll" />
      </v-col>
      <v-divider vertical></v-divider>
      <v-col class="pt-1">
        <v-icon small color="purple">mdi-food</v-icon
        ><span class="ml-3 text-color" style="font-size: 11px">Food</span>
        <div class="pt-3 py-3">
          <table v-if="foodOrdersCount" style="width: 100%">
            <tr
              v-for="(item, index) in [
                {
                  color: `blue`,
                  text: `Breakfast`,
                  value: foodOrdersCount.breakfast,
                },
                {
                  color: `green`,
                  text: `Lunch`,
                  value: foodOrdersCount.lunch,
                },
                {
                  color: `orange`,
                  text: `Dinner`,
                  value: foodOrdersCount.dinner,
                },
              ]"
              :key="index"
            >
              <td style="font-size: 11px" class="text-color border-bottom">
                {{ item.text }}
              </td>
              <td
                style="font-size: 11px"
                class="text-color border-bottom text-right"
              >
                <span>
                  {{ item.value == 0 ? 0 : parseInt(item.value) }}
                </span>
              </td>
            </tr>
          </table>
        </div>
      </v-col>
    </v-row>
    <v-row no-gutters class="mt-10">
      <v-col v-for="(stat, index) in history" :key="index">
        <v-divider></v-divider>

        <div cols="pt-15" style="display: flex">
          <v-icon small class="mx-1" :color="stat.color">{{
            stat.icon
          }}</v-icon>
          <small
            style="font-size: 9px; padding-top: 2px"
            class="px-2"
            :class="`${stat.color}--text`"
          >
            {{ stat.value }}
          </small>
        </div>
        <v-divider></v-divider>
      </v-col>
    </v-row>
  </span>
</template>
<script>
import CheckIn from "../components/booking/CheckIn.vue";
import CheckOut from "../components/booking/CheckOut.vue";
import NewCheckIn from "../components/booking/NewCheckIn.vue";
import ReservationList from "../components/reservation/ReservationList.vue";
import Available from "../components/svg/Available.vue";
import Dirty from "../components/svg/Dirty.vue";
import Booked from "../components/svg/Booked.vue";
import CheckOutSvg from "../components/svg/CheckOutSvg.vue";
import PaidBookedSvg from "../components/svg/PaidBookedSvg.vue";
import ExpectCheckInSvg from "../components/svg/ExpectCheckInSvg.vue";
import ExpectCheckOutSvg from "../components/svg/ExpectCheckOutSvg.vue";

export default {
  layout({ $auth }) {
    if ($auth.user.user_type != "company" && $auth.user.is_verified == 0) {
      return "guest";
    } else {
      return "default";
    }
  },

  components: {
    ExpectCheckOutSvg,
    ExpectCheckInSvg,
    CheckOutSvg,
    Booked,
    Available,
    ReservationList,
    CheckIn,
    CheckOut,
    NewCheckIn,
    Dirty,
    PaidBookedSvg,
  },
  data() {
    return {
      history: [
        {
          icon: "mdi-walk",
          value: "00",
          label: "WALKING",
          col: 7,
          color: "green",
        },
        {
          icon: "mdi-laptop",
          value: "00",
          label: "OTA",
          col: 7,
          color: "blue",
        },
        {
          icon: "mdi-account-tie",
          value: "00",
          label: "Corporate",
          col: 7,
          color: "orange",
        },
        {
          icon: "mdi-cloud-outline",
          value: "00",
          label: "WebSite",
          col: 7,
          color: "purple",
        },
        {
          icon: "mdi-gift-outline",
          value: "00",
          label: "Complimentary",
          col: 7,
          color: "pink",
        },
        {
          icon: "mdi-account-outline",
          value: "00",
          label: "Travel Agent",
          col: 7,
          color: "teal",
        },
      ],
      cards: [
        {
          color: "white",
          bgColor: "#d9534f",
          label: "Checkin",
          value: "15",
          sub_value: "4",
        },
        {
          color: "black",
          bgColor: "#ddbc91",
          label: "Checkout",
          value: "15",
          sub_value: "4",
        },
        {
          color: "black",
          bgColor: "#f5ece3",
          label: "Continue",
          value: "15",
        },
        {
          color: "white",
          bgColor: "#75a29f",
          label: "Vacant",
          value: "15",
        },
      ],
      isActiveTab: 1,
      BookingQuickCheckInCompKey: 1,
      calenderColorCodes: [],
      tab: 0,
      filterDate: "2024-08-15",
      menu2: false,
      colors: ["#92d050", "#ff0000", "#ffc000", "#0D652D", "#174EA6"],
      key: 1,
      reservation: [],
      rightClickRoomId: "",
      selected_booked_room_id: "",
      selected_booking_id: "",
      cancelCheckInDialog: false,
      checkInCancelReason: "",
      chart: {
        eco: 35,
      },

      check_out_menu: false,
      check_out: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
        .toISOString()
        .substr(0, 10),

      temp: "",
      isPageLoad: false,
      loading: false,
      cancelLoad: false,
      snackbar: false,
      response: "",
      isDirty: true,
      payingAdvance: false,

      ArrivalReportDialog: false,
      CheckOutReportDialog: false,
      InHouseDialog: false,
      FoodDialog: false,
      checkInDialog: false,
      checkInKey: 1,
      checkOutDialog: false,
      GRCDialog: false,
      postingDialog: false,
      viewPostingDialog: false,
      cancelDialog: false,
      NewBooking: false,

      formTitle: "",
      selectedItem: 0,
      showMenu: false,
      showMenuForNewBooking: false,

      bookingStatus: 0,
      eventStatus: "",
      x: 0,
      y: 0,

      elevations: [6, 12, 18],
      first_login_auth: 1,
      loading: true,

      logs: [],

      orders: "",
      products: "",
      customers: "",
      daily_orders: "",
      weekly_orders: "",
      monthly_orders: "",
      evenIid: "",
      eventStatus: "",
      rooms: [],
      postings: [],
      dirtyRoomsList: [],
      availableRooms: [],
      Occupied: [],
      checkIn: [],
      checkOut: [],
      reservedWithoutAdvance: [],
      reason: "",
      totalTransactionAmount: 0,
      new_payment: 0,
      new_advance: 0,
      AdvancePayLoading: false,
      reference: 0,
      full_payment: 0,
      isPrintInvoice: false,
      items: [],
      transactions: [],
      checkData: {},
      roomData: null,
      customerId: "",
      bookingId: "",
      document: null,
      lastTapTime: null,
      isDbCLick: false,
      members: {
        adult: 0,
        child: 0,
        total: 0,
      },
      foodOrdersCount: null,
      expectCheckOut: "",
      headers: [
        { text: "#" },
        { text: "Bill Number" },
        { text: "Room No" },
        { text: "Room Type" },
        { text: "Customer" },
        { text: "Item" },
        { text: "QTY" },
        { text: "Amount" },
        { text: "Date" },
      ],
      newBookingRoom: {},
      isIndex: true,

      showMenu: false,

      filtered: {
        AvailableRooms: [],
      },

      searchQuery: null,
      filterQuery: ``,
      keyTabAll: 11,
      keyTabexpected_arrival: 12,
      keyTabdirty: 13,
      keyTabblocked: 18,
      keyTabavailable: 14,
      keyTabcompliment: 15,
      keyTabdirty: 16,
      keyTabOccupied: 17,
    };
  },
  watch: {
    searchQuery() {
      this.keyTabAll++;
    },
    filterDate() {
      this.keyTabAll++;
      this.room_list();
    },
    tab() {},
    checkInDialog() {
      this.formTitle = "Check In";
      this.get_data();
      ++this.checkInKey;
      this.checkInDialog ? (this.isIndex = false) : (this.isIndex = true);
    },

    NewBooking() {
      this.NewBooking ? (this.isIndex = false) : (this.isIndex = true);
    },

    postingDialog() {
      this.formTitle = "Posting";
      this.get_data();
    },

    checkOutDialog() {
      this.formTitle = "Check Out";
      this.get_data();
    },

    viewPostingDialog() {
      this.formTitle = "View Post";
      this.get_posting();
    },

    payingAdvance() {
      this.formTitle = "Advance Payment";
      this.get_data();
    },
  },
  created() {
    this.filterDate = new Date(
      Date.now() - new Date().getTimezoneOffset() * 60000
    )
      .toISOString()
      .substr(0, 10);
    this.room_list();
    this.first_login_auth = this.$auth.user.first_login;

    setInterval(() => {
      this.room_list();
      this.key = this.key + 1;
    }, 1000 * 60 * 2);

    let payload = {
      params: {
        company_id: this.$auth.user.company.id,
      },
    };
    this.$axios.get(`room-color-codes`, payload).then(({ data }) => {
      this.calenderColorCodes = data;
    });

    setTimeout(() => {
      this.key += 1;
    }, 1000);
  },

  methods: {
    handleSuccess(message) {
      this.room_list();
      this.alert("Success!", message, "success");
      this.checkInDialog = false;
      this.keyTabAll++;
    },

    handleNewSuccess() {
      this.room_list();
      this.BookingQuickCheckInCompKey += 1;
      this.keyTabAll++;
    },
    refreshRoomList() {
      this.room_list();
      this.keyTabAll++;
    },

    get_next_day() {
      // const today = new Date();
      // const tomorrow = new Date(today);
      // tomorrow.setDate(tomorrow.getDate() + 1);
      // this.check_out_date = tomorrow.toISOString().substr(0, 10);

      const tomorrow = new Date();
      tomorrow.setDate(tomorrow.getDate() + 1);
      const year = tomorrow.getFullYear();
      const month = String(tomorrow.getMonth() + 1).padStart(2, "0");
      const day = String(tomorrow.getDate()).padStart(2, "0");
      const formattedDate = `${year}-${month}-${day}`;

      return formattedDate;
    },
    goToBookingPage() {
      console.log(" this.newBookingRoom", this.newBookingRoom);
      let currentDate = new Date(
        Date.now() - new Date().getTimezoneOffset() * 60000
      )
        .toISOString()
        .substr(0, 10);

      this.reservation.isCalculate = true;
      this.reservation.room_id = this.newBookingRoom.id;
      this.reservation.room_type = this.newBookingRoom.room_type.name;
      this.reservation.room_no = this.newBookingRoom.room_no;
      this.reservation.check_in = currentDate;
      this.reservation.booking_status = 2;

      this.reservation.check_out = this.get_next_day();

      let payload = {
        params: {
          company_id: this.$auth.user.company.id,
          roomType: this.reservation.room_type,
          room_no: this.reservation.room_no,
          checkin: this.reservation.check_in,
          checkout: this.reservation.check_out,
        },
      };

      this.$store.commit("booking_payload", payload);
      this.$axios
        .get(`get_data_by_select_with_tax`, payload)
        .then(({ data }) => {
          if (!data.status) {
            this.alert("Failure!", data.data, "error");
            return false;
          }

          this.reservation.room_id = data.room.id;
          this.reservation.price = data.total_price;
          this.reservation.priceList = data.data;
          this.reservation.total_tax = data.total_tax;

          this.reservation.total_price_after_discount =
            data.total_price_after_discount;
          this.reservation.total_price = data.total_price;
          this.reservation.total_discount = data.total_discount;

          let commitObj = {
            ...this.reservation,
          };
          //console.log('reservation1', commitObj);
          this.$store.commit("reservation", commitObj);
          this.$router.push(`/hotel/new2`);
        });
    },
    async logout() {
      this.$axios.get(`/logout`).then(({ res }) => {
        this.$auth.logout();
        this.$router.push(`/login`);
      });
    },

    can() {
      if (
        this.$auth.user.employee_role_id > 0 &&
        this.$auth.user.is_verified == 0
      ) {
        this.logout();
        this.$router.push(`login`);
        return false;
      } else {
        return true;
      }
    },

    handleTouchstart(event, room) {
      console.log(room);
      this.touchstart(
        event,
        room?.booked_room?.id,
        room?.booked_room?.booking?.booking_status
      );
    },
    handleMouseOver(room) {
      this.mouseOver(
        room?.booked_room?.id,
        room?.booked_room?.booking?.booking_status
      );
    },
    getButtonClass(room) {
      return room.booked_room?.background ===
        "linear-gradient(135deg, #4390FC 0, #4390FC 100%)"
        ? "element"
        : "";
    },
    getBackgroundImage(room) {
      return room?.booked_room?.background || "";
    },
    isDeviceStatusActive(room) {
      return room.device?.latest_status === 1;
    },

    caps(str) {
      if (str == "" || str == null) {
        return "---";
      } else {
        let res = str.toString();
        return res.replace(/\b\w/g, (c) => c.toUpperCase());
      }
    },

    get_check_out() {
      this.checkOutDialog = true;
      this.get_transaction();
    },

    get_transaction() {
      let id = this.bookingId;
      let payload = {
        params: {
          company_id: this.$auth.user.company.id,
        },
      };
      this.$axios
        .get(`get_transaction_by_booking_id/${id}`, payload)
        .then(({ data }) => {
          this.transactions = data.transactions;
          this.totalTransactionAmount = data.totalTransactionAmount;
        });
    },

    mouseOver(bookedRoomId, bookingStatus) {
      this.evenIid = bookedRoomId;
      this.bookingStatus = bookingStatus;
    },

    touchstart(e, bookedRoomId, bookingStatus) {
      this.evenIid = bookedRoomId;
      this.bookingStatus = bookingStatus;
      this.show(e, true);
    },

    mouseOverForAvailable(newBookingRoom) {
      // this.newBookingRoom = newBookingRoom;
      // console.log(newBookingRoom);
    },

    closeNewCheckin() {
      this.newBookingRoom = false;
      this.NewBooking = false;
    },

    get_data(jsEvent = null) {
      this.selected_booked_room_id = this.evenIid;

      let payload = {
        params: {
          id: this.evenIid,
          company_id: this.$auth.user.company.id,
        },
      };
      this.rightClickRoomId = "---";
      this.$axios.get(`get_booked_room`, payload).then(({ data }) => {
        let { booking, ...roomData } = data;
        this.checkData = data.booking;
        this.roomData = roomData;
        this.bookingId = data.booking.id;

        this.rightClickRoomId = data.booking.resourceId;

        this.full_payment = "";
        this.bookingStatus = data.booking_status;
        this.customerId = data.booking.customer_id;
        if (this.isDbCLick) {
          this.get_event_by_db_click();
        }
      });
    },

    show(e, isTouch = false) {
      this.showMenuForNewBooking = false;
      e.preventDefault();
      this.get_data();
      if (isTouch) {
        const currentTime = new Date().getTime();
        const tapThreshold = 300; // milliseconds
        if (this.lastTapTime && currentTime - this.lastTapTime < tapThreshold) {
          this.$router.push(`/customer/details/${this.bookingId}`);
          return;
        }
        this.lastTapTime = currentTime;
      }

      if (isTouch) {
        const touch = e.touches[0];
        this.x = touch.clientX;
        this.y = touch.clientY;
      } else {
        this.x = e.clientX;
        this.y = e.clientY;
      }
      this.$nextTick(() => {
        this.showMenu = true;
      });
    },

    makeNewBookingForTouch(e, newBookingRoom) {
      this.newBookingRoom = newBookingRoom;
      this.showMenu = false;
      e.preventDefault();
      const touch = e.touches[0];
      this.x = touch.clientX;
      this.y = touch.clientY;
      this.$nextTick(() => {
        this.showMenuForNewBooking = true;
      });
    },

    makeNewBooking(e, newBookingRoom) {
      this.newBookingRoom = newBookingRoom;

      e.preventDefault();
      this.x = e.clientX;
      this.y = e.clientY;
      this.$nextTick(() => {
        this.showMenuForNewBooking = true;
      });
    },

    roomStatus(status) {
      let payload = {
        company_id: this.$auth.user.company.id,
        room_no: this.newBookingRoom.room_no,
      };
      this.$axios
        .post(`set_room_status/${status}`, payload)
        .then(({ data }) => {
          if (!data.status) {
            this.snackbar = data.status;
            this.response = data.message;
            return;
          }
          this.room_list();
          this.snackbar = data.status;
          this.response = data.message;
        })
        .catch((err) => console.log(err));
    },

    get_posting() {
      let id = this.evenIid;
      let payload = {
        params: {
          company_id: this.$auth.user.company.id,
        },
      };
      this.$axios.get(`posting/${id}`, payload).then(({ data }) => {
        this.postings = data;
      });
    },
    alert(title = "Success!", message = "hello", type = "error") {
      this.$swal(title, message, type);
    },
    room_list() {
      let payload = {
        params: {
          company_id: this.$auth.user && this.$auth.user.company.id,
          // check_in: new Date().toJSON().slice(0, 10),
          check_in: this.filterDate,
          filter_date: this.filterDate,
        },
      };
      this.$axios.get(`room_list_grid`, payload).then(({ data }) => {
        if (!data.status) {
          this.alert("Failure!", data.data, "error");
          return false;
        }

        this.rooms = data;
        this.availableRooms = data.availableRooms;
        this.reservedWithoutAdvance = data.reservedWithoutAdvance;
        this.Occupied = data.checkIn;
        this.expectCheckOut = data.expectCheckOut;
        this.dirtyRoomsList = data.dirtyRoomsList;

        let data1 = data.reservedWithoutAdvance.map((e) => e.room_no);
        let data2 = data.expectCheckOut.map((e) => e.room_no);
        let data3 = data.checkIn.map((e) => e.room_no);
        let data4 = data.blockedRooms.map((e) => e.room_no);
        let data5 = data.dirtyRoomsList.map((e) => e.room_no);

        let allRoomNumbers = [...data1, ...data2, ...data3, ...data4, ...data5];
        let uniqueRoomNumbers = [...new Set(allRoomNumbers)];
        this.availableRooms = data.availableRooms.filter(
          (e) => !uniqueRoomNumbers.includes(e.room_no)
        );

        this.members = data.members;
        this.foodOrdersCount = data.foodOrdersCount;

        this.isIndex = true;
        setTimeout(() => {
          this.isPageLoad = true;
        }, 100);
        this.keyTabAll = 31;
        this.keyTabexpected_arrival = 33;
        this.keyTabdirty = 34;
        this.keyTabblocked = 35;
        this.keyTabavailable = 36;
        this.keyTabcompliment = 37;
        this.keyTabdirty = 38;
        this.keyTabOccupied = 39;
      });
    },

    dblclick() {
      this.isDbCLick = true;
      this.get_data();
    },

    viewBillingDialog() {
      let id = this.bookingId;
      this.$router.push(`/customer/details/${id}`);
    },

    get_event_by_db_click() {
      this.$router.push(`/customer/details/${this.bookingId}`);
    },
    changeCheckInAdminProcess() {
      if (this.$auth.user.role.name.toLowerCase() != "admin") {
        //alert("You are not authorized to Cancel the Checkin");

        this.alert(
          "Failure!",
          "You are not authorized to Cancel the Checkin",
          "error"
        );
        return false;
      } else {
        if (this.checkInCancelReason == "") {
          alert("Enter reason");
          return;
        }

        this.cancelLoad = true;

        let payload = {
          cancel_checkin_reason: this.checkInCancelReason,
          cancel_checkin_userid: this.$auth.user.id,
          booking_id: this.bookingId,
          company_id: this.$auth.user.company.id,
          booked_room_id: this.selected_booked_room_id,
        };

        this.$axios
          .post(`change_checkin_to_booking_admin/${this.evenIid}`, payload)
          .then(({ data }) => {
            if (!data.status) {
              this.snackbar = data.status;
              this.response = data.message;
              this.cancelLoad = false;
              return;
            }
            this.cancelLoad = false;
            this.room_list();
            this.reason = "";
            this.cancelDialog = false;
            this.snackbar = data.status;
            this.response = data.message;
            this.cancelCheckInDialog = false;
          })
          .catch((err) => console.log(err));
      }
    },
    setAvailable() {
      let payload = {
        cancel_by: this.$auth.user.id,
        bookedRoomId: this.evenIid,
      };

      this.$axios
        .post(`set_available/${this.bookingId}`, payload)
        .then(({ data }) => {
          if (!data.status) {
            this.snackbar = data.status;
            this.response = data.message;
            return;
          }
          this.room_list();
          this.cancelDialog = false;
          this.snackbar = data.status;
          this.response = data.message;
        })
        .catch((err) => console.log(err));
    },

    setMaintenance() {
      let payload = {
        cancel_by: this.$auth.user.id,
      };
      this.$axios
        .post(`set_maintenance/${this.bookingId}`, payload)
        .then(({ data }) => {
          if (!data.status) {
            this.snackbar = data.status;
            this.response = data.message;
            return;
          }
          this.room_list();
          this.cancelDialog = false;
          this.snackbar = data.status;
          this.response = data.message;
        })
        .catch((err) => console.log(err));
    },

    cancelItem() {
      if (this.reason == "") {
        alert("Enter reason");
        return;
      }

      this.cancelLoad = true;

      let payload = {
        reason: this.reason,
        cancel_by: this.$auth.user.id,
      };
      this.$axios
        .post(`cancel_room/${this.evenIid}`, payload)
        .then(({ data }) => {
          if (!data.status) {
            this.snackbar = data.status;
            this.response = data.message;
            this.cancelLoad = false;
            return;
          }
          this.cancelLoad = false;
          this.room_list();
          this.reason = "";
          this.cancelDialog = false;
          this.snackbar = data.status;
          this.response = data.message;
        })
        .catch((err) => console.log(err));
    },

    succuss(
      data,
      check_in = true,
      posting = true,
      check_out = true,
      advance_payment = true
    ) {
      if (check_in) {
        this.checkData = {};
        this.checkInDialog = false;
        this.new_payment = 0;
      }
      if (check_out) {
        this.checkData = {};
        this.checkOutDialog = false;
      }
      if (posting) {
        this.posting = {};
        this.postingDialog = false;
      }

      if (advance_payment) {
        this.checkData = {};
        this.new_advance = 0;
        this.payingAdvance = false;
      }

      this.room_list();
      this.errors = [];
      this.loading = false;
      this.snackbar = true;
      this.response = data.message;
    },

    close() {
      this.checkInDialog = false;
      this.new_payment = 0;
      this.new_advance = 0;
      this.payingAdvance = false;
      this.checkOutDialog = false;
      this.document = null;
    },

    closeCheckOut() {
      this.checkOutDialog = false;
    },

    closeDialogs(res) {
      this.succuss(res);
    },
  },
};
</script>
