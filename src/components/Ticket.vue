<template>
  <div>
    <div v-for="(res, i) in result.itineraries" :key="i">
      <div class="ticket" v-for="(ticket, j) in resp(res)" :key="j">
        <div class="ticket__info">
          <div class="ticket__info__inner">
            <div class="ticket__carrier-info">
              <div class="ticket__carrier-logo">
                  <img :src="icon(ticket.carrier)" alt="">
              </div>
              <div class="ticket__carrier-name">{{ticket.carrier_name}}</div>
            </div>
            <div class="ticket__carrier-dep_date">
              <div class="ticket__carrier-dep_date date">
                <h4>{{flightDate(ticket.segments[0].dep_time)}}</h4>
              </div>
              <div class="ticket__carrier-dep_date time">
                <h3>{{flightTime(ticket.segments[0].dep_time)}}</h3>
              </div>
            </div>
            <div class="ticket__carrier-flight">
              <div class="ticket__carrier-flight__travel">
                <div class="ticket__carrier-flight__travel-from">
                  <span>{{ticket.segments[0].origin_code}}</span>
                </div>
                <div class="ticket__carrier-flight__travel-time">
                  <p>{{travelTime(ticket.traveltime)}}</p>
                </div>
                <div class="ticket__carrier-flight__travel-to">
                  <span>{{ticket.segments[ticket.segments.length-1].dest_code}}</span>
                </div>
              </div>
              <div class="ticket__carrier-flight__line">
                <div class="circle one"></div>
                <div class="circle two"></div>
                <div class="circle three"></div>
              </div>
              <div class="ticket__carrier-flight__through">
                <p
                  :style="{color: activeColor}"
                >{{`${flight(ticket.segments)}${dockingTime(ticket.segments)}`}}</p>
              </div>
            </div>
            <div class="ticket__carrier-arr_date">
              <div class="ticket__carrier-arr_date date">
                <h4>{{flightDate(ticket.segments[ticket.segments.length-1].arr_time)}}</h4>
                <p>{{timeZone(ticket.segments)}}</p>
              </div>
              <div class="ticket__carrier-arr_date time">
                <h3>{{flightTime(ticket.segments[ticket.segments.length-1].arr_time)}}</h3>
              </div>
            </div>
          </div>
          <div class="ticket__info-details">
            <div class="ticket__info-conditions">
              <span>Детали перелета</span>
              <span>Условия тарифа</span>
            </div>
            <div class="ticket__info-luggage">
              <img v-if="!ticket.refundable" src="../assets/group.png" />
              <span>{{ticket.refundable ? 'возвратный' : 'невозвратный'}}</span>
                
            </div>
          </div>
        </div>
        <div class="ticket__price">
          <h3>{{`${ticket.price.amount} ₸`}}</h3>
          <button>Выбрать</button>
          <p>Цена за всех пассажиров</p>
          <div class="ticket__price-details">
            <p>{{baggageOption(ticket)}}</p>
            <span>+ Добавить багаж</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["result"],
  data() {
    return {
      activeColor: ""
    };
  },
  methods: {
    resp: function(ticket) {
      return ticket.map(item => item);
    },
    flightDate: function(date) {
      const result = date.toLowerCase().split(" ");
      return `${result[0]} ${result[1]} ${result[2]}`;
    },
    flightTime: function(date) {
      const result = date.split(" ");
      return `${result[3]}`;
    },
    flight: function(flight) {
      let str = "";
      if (flight.length === 1) {
        this.activeColor = "#55bb06";
        str = "Прямой рейс";
      } else if (flight.length === 2) {
        this.activeColor = "#FF9900";
        str = `Через ${flight[0].airport_dest}`;
      } else if (flight.length > 2) {
        this.activeColor = "#FF9900";
        str = `${flight.length - 1} пересадки`;
      }
      return str;
    },
    travelTime: function(time) {
      const hours = Math.floor(time / 60 / 60);
      const minutes = Math.floor(time / 60) - hours * 60;
      return `${hours} ч ${minutes} м`;
    },
    dockingTime: function(segments) {
      if (segments.length === 2) {
        const arrTime = new Date(segments[0].arr_time_iso);
        const depTime = new Date(segments[segments.length - 1].dep_time_iso);
        let res = (depTime.getTime() - arrTime.getTime()) / 1000;
        return `, ${this.travelTime(res)}`;
      } else {
        return "";
      }
    },
    timeZone: function(timeZone) {
      const timeZoneFrom = timeZone[0].dep_time_iso;
      const timeZoneTo = timeZone[timeZone.length - 1].arr_time_iso;
      const result =
        Number.parseInt(
          timeZoneFrom.substr(timeZoneFrom.indexOf("+") + 1).replace(":", ".")
        ) -
        Number.parseInt(
          timeZoneTo.substr(timeZoneTo.indexOf("+") + 1).replace(":", ".")
        );
      return `+${result}`;
    },
    icon: function(carrier) {
        return `https://aviata.kz/static/airline-logos/80x80/${carrier}.png`;
    },
    baggageOption: function(baggage) {
        let str = '';
        if(baggage.segments[0].baggage_options[0].value === 0){
            str = 'нет багажа';
        }else{
            str = `${baggage.segments[0].baggage_options[0].value} ${baggage.segments[0].baggage_options[0].unit}`
        }
        return str;
    }
  }
};
</script>

<style scoped>
span {
  font-size: 10px;
  line-height: 12px;
  color: #b9b9b9;
}
p {
  font-size: 12px;
  line-height: 18px;
  color: #202123;
  margin: 0;
}
h3 {
  font-weight: 600;
  font-size: 24px;
  line-height: 33px;
  margin: 0;
}
h4 {
  font-size: 12px;
  line-height: 16px;
  margin: 0;
}
.ticket {
  width: 880px;
  height: 168px;
  background: #ffffff;
  margin: 12px 0;
  box-shadow: 0px 2px 4px rgba(0, 0, 0, 0.15);
  border-radius: 4px;
  display: flex;
}
.ticket__info {
  width: 75%;
  padding: 50px 40px 8px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.ticket__price {
  width: 25%;
  background: #f5f5f5;
  border-radius: 0px 4px 4px 0px;
}
.ticket__info__inner {
  display: flex;
  align-items: center;
}
.ticket__carrier-info {
  display: flex;
  width: 8rem;
  align-items: center;
}
.ticket__carrier-name {
  font-weight: 600;
  font-size: 14px;
  line-height: 19px;
  padding-right: 20px;
}
.ticket__carrier-flight {
  padding: 0 30px;
  width: 12rem;
}
.ticket__carrier-flight__travel {
  display: flex;
  justify-content: space-between;
  width: 100%;
}
.ticket__carrier-flight__line {
  margin: 5px 0;
  border: 1px solid #b9b9b9;
  position: relative;
  display: flex;
  justify-content: space-between;
}
.circle {
  position: absolute;
  border: 1px solid #b9b9b9;
  background-color: #fff;
  height: 5px;
  width: 5px;
  top: -3.5px;
  border-radius: 50%;
}
.circle.one {
  left: -2px;
}
.circle.two {
  left: 48%;
}
.circle.three {
  right: -2px;
}
.ticket__info-details {
  text-align: left;
  display: flex;
}
.ticket__info-conditions {
  padding-right: 23px;
}
.ticket__info-conditions span {
  font-size: 12px;
  line-height: 16px;
  color: #7284e4;
  border-bottom: 1px dashed #7284e4;
  margin-right: 23px;
  cursor: pointer;
}
.ticket__carrier-arr_date.date {
  display: flex;
}
.ticket__carrier-arr_date.date p {
  font-weight: 600;
  font-size: 10px;
  line-height: 14px;
  color: rgba(255, 55, 36, 0.8);
  padding-left:5px;
}

.ticket__info-luggage {
  display: flex;
  align-items: center;
}
.ticket__info-luggage span {
  font-size: 12px;
  line-height: 14px;
  color: #707276;
  margin: 0 7px;
}
.ticket__price {
  padding: 12px 20px;
}
.ticket__price h3 {
  font-weight: 400;
  font-family: Arial;
  font-size: 24px;
  line-height: 28px;
  color: #202123;
}
.ticket__price button {
  background: #55bb06;
  border-radius: 4px;
  font-weight: bold;
  font-size: 18px;
  line-height: 25px;
  color: #ffffff;
  border: none;
  width: 100%;
  padding: 8px 0;
  margin: 10px 0;
  cursor: pointer;
}
.ticket__price p {
  color: #707276;
  font-size: 12px;
  line-height: 16px;
}
.ticket__price-details {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 12px;
}
.ticket__price-details p {
  color: #202123;
}
.ticket__price-details span {
  color: #5763b3;
  font-weight: 600;
  font-size: 12px;
  line-height: 16px;
  background: #eaf0fa;
  border-radius: 2px;
  padding: 3px 5px;
  cursor: pointer;
}
.ticket__carrier-logo{
    width: 24px;
    height: 24px;
    margin-right: 10px;
}
.ticket__carrier-logo img{
    width: 100%;
}
</style>