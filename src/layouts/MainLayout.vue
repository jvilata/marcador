<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn round flat>
          <q-avatar size="40px">
            <q-img src="~assets/VIDA_color.png"/>
          </q-avatar>
        </q-btn>
        <div class="items-center no-wrap absolute-center">
          <div class="text-subtitle1">Marcador de Juegos</div>
        </div>
        <div class="q-gutter-sm q-pr-md row items-center no-wrap absolute-right">
          v1.0
        </div>
      </q-toolbar>
    </q-header>
    <q-page-container>
    <q-item class="row">
    <!-- GRID. en row-key ponemos la columna del json que sea la id unica de la fila -->
    <q-table
      class="col GridMarcador-table"
      virtual-scroll
      :pagination.sync="pagination"
      :rows-per-page-options="[0]"
      :virtual-scroll-sticky-size-start="48"
      row-key="id"
      :data="registrosSeleccionados"
      :columns="columns"
      table-style="max-height: 70vh; max-width: 93vw"
    >

      <template v-slot:header="props">
        <!-- CABECERA DE LA TABLA -->
        <q-tr :props="props">
          <q-th>
            <div style="max-width: 10px">
            </div>
          </q-th>
          <q-th
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
          >
            {{ col.label }}
          </q-th>
        </q-tr>
      </template>

      <template v-slot:body="props">
        <q-tr :props="props" :key="`m_${props.row.id}`" @mouseover="rowId=`m_${props.row.id}`">
          <q-td>
            <!-- columna de acciones: editar, borrar, etc -->
            <div style="max-width: 10px">
            <q-btn flat v-if="rowId===`m_${props.row.id}`"
              @click.stop="deleteRecord(props.row.id)"
              round
              dense
              size="sm"
              color="red"
              icon="delete">
              <q-tooltip>Borrar</q-tooltip>
            </q-btn>
            </div>
          </q-td>
          <q-td
            v-for="col in props.cols"
            :key="col.name"
            :props="props"
          >
            <div :style="col.style">
              {{ col.value }}
            </div>
            <q-popup-edit v-if="!['total'].includes(col.name)"
                v-model="props.row[col.name]"
                max-height="600px"
                buttons
                @save="updateRecord(props.row)">
                <!-- aqui definimos las ediciones especificas para cada columna -->
                <q-input v-model="props.row[col.name]"
                  autofocus
                  clearable
                  style="width: 100px;"
                />
            </q-popup-edit>
          </q-td>
        </q-tr>
      </template>
      <template v-slot:no-data>
        <div class="absolute-bottom q-mb-sm" style="left: 30vw">
          <q-btn
            @click.stop="addRecord"
            dense
            color="primary"
            size="10px"
            label="Jugador"
            icon="add">
            <q-tooltip>Añadir Jugador</q-tooltip>
          </q-btn>
          <q-btn
            @click.stop="addPartida"
            dense
            color="primary"
            size="10px"
            label="Partida"
            icon="add">
            <q-tooltip>Añadir Partida</q-tooltip>
          </q-btn>
           <q-btn
            @click.stop="limpiarPartidas"
            dense
            color="primary"
            size="10px"
            label="Partida"
            icon="restore">
            <q-tooltip>Limpiar Partida</q-tooltip>
          </q-btn>
        </div>
        <div>
          0 Jug.
        </div>
      </template>
      <template v-slot:bottom>
        <div class="absolute-bottom q-mb-sm" style="left: 20vw">
          <q-btn
            @click.stop="addRecord"
            dense
            color="primary"
            label="Jugador"
            size="10px"
            icon="add">
            <q-tooltip>Añadir Jugador</q-tooltip>
          </q-btn>
          <q-btn class="q-ml-sm"
            @click.stop="addPartida"
            dense
            color="primary"
            size="10px"
            label="Partida"
            icon="add">
            <q-tooltip>Añadir Partida</q-tooltip>
          </q-btn>
           <q-btn class="q-ml-sm"
            @click.stop="limpiarPartidas"
            dense
            color="primary"
            size="10px"
            label="Limpiar Part"
            icon="restore">
            <q-tooltip>Empezar</q-tooltip>
          </q-btn>
        </div>
        <div class="text-caption">
          {{ registrosSeleccionados.length + ' Jug.' }}
        </div>
      </template>
    </q-table>
    </q-item>
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',
  data () {
    return {
      rowId: '',
      registrosSeleccionados: [
        {
          id: 0,
          jugador: 'jugador0',
          total: 0,
          partida1: '',
          partida2: '',
          partida3: '',
          partida4: '',
          partida5: '',
          partida6: '',
          partida7: '',
          partida8: '',
          partida9: '',
          partida10: ''
        }
      ],
      columns: [
        { name: 'jugador', required: true, label: 'Jugador', sortable: true, align: 'center', field: 'jugador' },
        { name: 'total', required: true, label: 'Total', sortable: true, align: 'right', field: 'total' },
        { name: 'partida1', required: true, label: 'Partida 1', sortable: true, align: 'right', field: 'partida1' }
      ],
      pagination: { rowsPerPage: 0 }
    }
  },
  methods: {
    addRecord () {
      var id = 0
      if (this.registrosSeleccionados.length === 0) id = 0
      else id = this.registrosSeleccionados[this.registrosSeleccionados.length - 1].id + 1
      var record = {
        id: id,
        jugador: 'jugador' + this.registrosSeleccionados.length,
        total: 0,
        partida1: '',
        partida2: '',
        partida3: '',
        partida4: '',
        partida5: '',
        partida6: '',
        partida7: '',
        partida8: '',
        partida9: '',
        partida10: ''
      }
      this.registrosSeleccionados.push(record)
    },
    deleteRecord (id) {
      this.registrosSeleccionados.splice(this.registrosSeleccionados.findIndex(row => row.id === id), 1)
    },
    updateRecord (row) {
      row.total = +row.partida1 + +row.partida2 + +row.partida3 + +row.partida4 + +row.partida5 + +row.partida6 + +row.partida7 + +row.partida8 + +row.partida9 + +row.partida10
    },
    addPartida () {
      var column = {
        name: 'partida' + (this.columns.length - 1),
        required: true,
        label: 'Partida ' + (this.columns.length - 1),
        align: 'right',
        sortable: true,
        field: 'partida' + (this.columns.length - 1)
      }
      this.columns.splice(2, 0, column)
    },
    limpiarPartidas () {
      var column = {
        name: 'partida1',
        required: true,
        label: 'Partida 1',
        align: 'right',
        field: 'partida1'
      }
      this.columns.splice(2, this.columns.length, column)
      this.registrosSeleccionados.forEach(row => {
        row.total = 0
        row.partida1 = 0
        row.partida2 = 0
        row.partida3 = 0
        row.partida4 = 0
        row.partida5 = 0
        row.partida6 = 0
        row.partida7 = 0
        row.partida8 = 0
        row.partida9 = 0
        row.partida10 = 0
      })
    }
  }
}
</script>

<style lang="sass">
  .GridMarcador-table
    .q-table__top,
    .q-table__bottom,
    thead tr:first-child th
      /* bg color is important for th; just specify one */
      background-color: $indigo-1

    thead tr th
      position: sticky
      z-index: 1
    thead tr:first-child th
      top: 0

    td:nth-child(-n+2)
      background-color: $orange-1
    th:first-child
      position: sticky
      left: 0
      z-index: 3
    td:nth-child(-n+2)
      position: sticky
      left: 0
      z-index: 0

    /* this is when the loading indicator appears */
    &.q-table--loading thead tr:last-child th
      /* height of all previous header rows */
      top: 48px
</style>
