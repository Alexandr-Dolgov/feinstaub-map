<template lang="jade">
#cell-info(v-if="cell")
	a(href='#' onclick='document.getElementById("cell-info").style.display="none";return false;' style='color:white') (закрыть)
	p
		a(href='#' id='map-info-on' onclick='document.getElementById("map-info-on").style.display="none";document.getElementById("map-info-off").style.display="";document.getElementById("map-info").style.display=""; return false;' style='color:white') Показать пояснения
		a(href='#' id='map-info-off' onclick='document.getElementById("map-info-on").style.display="";document.getElementById("map-info-off").style.display="none";document.getElementById("map-info").style.display="none"; return false;' style='color:white; display:none;') Скрыть пояснения
	div(id='map-info' style='display:none')
		p В настоящее время плитки окрашены в соответствии со средним значением PM10 всех датчиков в ячейке. См. шкалу в левом нижнем углу.
		p Строка "среднее" содержит среднее значенее всех датчиков, содержащихся в ячейке. Числа в первом столбце после (+) соответствуют идентификаторам датчиков.
		p Обратите внимание: мы показываем на карте значения последних 5 минут. Значения, опубликованные соответствующими государственными органами, даются как 24-часовое среднее значение. В результате значения на карте могут значительно отличаться от этих средних значений за 24 часа.
		p Нажимая на плюс перед идентификатором датчика, можно отобразить 2 графика. График "24 h float" отображает 24-часовую скользящую среднюю за последние 7 дней. По техническим причинам в начале есть пробел в один день, поэтому на самом деле график показывает 8 дней, но первый пустой. Второй график "Last 24 hours" показывает данные за последние 24 часа.
		p PM10 и PM2.5 количество мелкодисперсных частиц соответственно около 10 микрометров в диаметре и меньше и 2.5 микрометров и меньше.
	h3 #Датчиков {{cell.length}}
	
	table
		tr
			th ID датчика
			th PM10 мкг/м³
			th PM2.5 мкг/м³
		tr.mean
			td среднее
			td {{mean.P1.toFixed(0)}}
			td {{mean.P2.toFixed(0)}}
		template(v-for="sensor in cell")
			tr
				td(style="text-align:left;")
					a(:id="'graph_'+sensor.o.id+'_on'" class="graph_on" onclick="var sensor=this.id.substring(0,this.id.length-3);document.getElementById(sensor).style.display='';document.getElementById(sensor+'_on').style.display='none';document.getElementById(sensor+'_off').style.display=''; document.getElementById('images_'+sensor.substr(6)).innerHTML='<img src=\"https://api.luftdaten.info/grafana/render/dashboard-solo/db/single-sensor-view?orgId=1&panelId=1&width=300&height=200&tz=UTC%2B02%3A00&var-node=' + sensor.substring(6) + '\" /><br /><img src=\"https://api.luftdaten.info/grafana/render/dashboard-solo/db/single-sensor-view?orgId=1&panelId=2&width=300&height=200&tz=UTC%2B02%3A00&var-node=' + sensor.substring(6) + '\" /><br /><br />'; return false;" href='#' style='color:white; text-decoration: none;') (+)&nbsp;{{sensor.o.id}}
					a(:id="'graph_'+sensor.o.id+'_off'" class="graph_off" onclick="var sensor=this.id.substring(0,this.id.length-4);document.getElementById(sensor).style.display='none';document.getElementById(sensor+'_on').style.display='';document.getElementById(sensor+'_off').style.display='none'; document.getElementById('images_'+sensor.substr(6)).innerHTML=''; return false;" href='#' style='color:white; text-decoration: none; display: none;') (-)&nbsp;{{sensor.o.id}}
				td {{sensor.o.data.P1.toFixed(0)}}
				td {{sensor.o.data.P2.toFixed(0)}}
			tr(:id = "'graph_'+sensor.o.id" style="display:none" class="cell_info_images")
				td(:id = "'images_'+sensor.o.id" colspan='3')
					br
</template>
<script>
import _ from 'lodash'

export default {
	data () {
		return {
		}
	},
	props: {
		'cell': Array
	},
	computed: {
		mean () {
			return {
				P1: _.meanBy(this.cell, (o) => o.o.data.P1),
				P2: _.meanBy(this.cell, (o) => o.o.data.P2)
			}
		}
	}
}
</script>
<style lang="stylus">
</style>
