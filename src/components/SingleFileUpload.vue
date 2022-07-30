<template>
  <div>
      <b-modal ref="my-modal-1" no-close-on-backdrop hide-footer title="Замнить на...">
        <div class="row">
          <div class="container-fluid px-0 col-10">            
            <div class="input-group mb-3">
              <div class="input-group-prepend">
                <span class="input-group-text">Фильтр</span>
              </div>
              <input type="text" v-model="filterName" class="form-control"> 
            </div>
          </div>
        </div>
        <div class="row justify-content-center">
          <div class="col-6 col-sm-4 col-md-3" v-for="(value, key, i) in filteredDB" :key="'db'+i">
            <img class="icon" :src="value.src" @click="changeXml(key)">
            <div class="mb-2"><small>{{ value.name }}</small></div>
          </div>
        </div>
      </b-modal>
      <div class="mb-3">
        <a  @click="openInNewTab('https://drive.google.com/file/d/1s2Jm7zH-NVei_mF_-6AO9LG4R-8cRHJn/view?usp=sharing')" href="">Тестовый файл сохранений</a>
      </div>
      <div class="row justify-content-center">
        <div class="col-12 col-sm-12 col-md-10 col-lg-8">
          <b-tabs lazy pills card content-class="mt-3" align="center">
          <b-tab title="Редактор сохранений">
            <div class="row justify-content-center">
              <div class="col-12 col-sm-12 col-md-10 col-lg-8 my-3">
                <b-form-file
                  v-model="file1"
                  :state="Boolean(file1)"
                  placeholder="Choose a file or drop it here..."
                  drop-placeholder="Drop file here..."
                ></b-form-file>
              </div>
            </div>
            <div class="row justify-content-center">
              <div class="col-10">
                <b-button-group v-if="file1" class="my-2">
                  <b-button v-b-tooltip.hover title="Получить данные из загруженного файла" variant="success" @click="parsXml()">Читать</b-button>
                  <b-button v-if="deckSpells.length" v-b-tooltip.hover title="Скачать изменённый файл" variant="warning" @click="saveXml()">Скачать</b-button>
                </b-button-group>
              </div>
            </div>
            <div  class="row justify-content-center">
              <div class="col-12" v-if="deckSpells.length">
                <nav aria-label="breadcrumb">
                  <ol class="breadcrumb">
                    <li class="breadcrumb-item active" aria-current="page">Колода</li>
                  </ol>
                </nav>
                <div class="row justify-content-center">
                  <div class="col-6 col-sm-4 col-md-3 col-lg-2" v-for="(dspell, i) in deckSpells" :key="'deckSpells'+i" >
                    <img class="icon" :src="dataBase[dspell.value]? dataBase[dspell.value].src : dataBaseUnknown.src" @click="openReplaceModal('deck', dspell.index)">
                    <div><small>{{ dspell.value }}</small></div>
                    <div class="mb-2"><small>{{ dataBase[dspell.value]?dataBase[dspell.value].name : dataBaseUnknown.name }}</small></div>
                  </div>
                </div>
              </div>
              <div class="col-12" v-if="bagSpells.length">

                <nav aria-label="breadcrumb">
                  <ol class="breadcrumb">
                    <li class="breadcrumb-item active" aria-current="page">Инвентарь</li>
                  </ol>
                </nav>
                <div class="row justify-content-center">
                  <div class="col-6 col-sm-4 col-md-3 col-lg-2" v-for="(bspell, i) in bagSpells" :key="'bagSpells'+i" >
                    <div>
                      <img class="icon" :src="dataBase[bspell.value]? dataBase[bspell.value].src : dataBaseUnknown.src" @click="openReplaceModal('bag', bspell.index)">
                      <div><small>{{ bspell.value }}</small></div>
                      <div class="mb-2"><small>{{ dataBase[bspell.value]?dataBase[bspell.value].name : dataBaseUnknown.name }}</small></div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </b-tab>
          <b-tab title="Список заклинаний">
            <div class="row">
              <div class="container-fluid px-0 col-10 col-sm-8 col-md-6">            
                <div class="input-group mb-3">
                  <div class="input-group-prepend">
                    <span class="input-group-text">Фильтр</span>
                  </div>
                  <input type="text" v-model="filterNameList" class="form-control"> 
                </div>
              </div>
            </div>
            <div class="row justify-content-center">
              <table>
                <tr v-for="(value, key, i) in filteredDBList" :key="'dbtt'+i">
                  <th>{{ i + 1 }}</th>
                  <th>{{ key }}</th>
                  <th>{{ value.name }}</th>
                  <th><img class="icon mt-1" :src="value.src"></th>
                </tr>
              </table>
            </div>
          </b-tab>
          </b-tabs>
        </div>
      </div>
  </div>
</template>


<script type="text/javascript" src="../assets/2.xml"></script>

<script>
  export default {
    name: 'SingleFileUploadComponent',
    data() {
      return {
        file1: null,
        xmlDoc: null,
        newSavefile: null,
        deck: null,
        bag: null,
        dataBase: null,
        dataBaseUnknown: null,
        locationMode: null,
        locationIndex: null,
        filterName: null,
        filterNameList: null,
      }

    },
    methods: {
      openInNewTab(url) {
        window.open(url, '_blank').focus();
      },
      getDB() {
        this.dataBaseUnknown = {
          name:'Неизвестное заклинание',
          src: require('../assets/UNKN.png')
        } 
        this.dataBase = {
          OUD1:{name:'Бесцветный жнец', src: require('../assets/OUD1.png')},
          OUD2:{name:'Бесцветный разрушитель', src: require('../assets/OUD2.png')},
          OUD3:{name:'Бесцветный убийца', src: require('../assets/OUD3.png')},
          OUE3:{name:'Бесцветный ловкач', src: require('../assets/OUE3.png')},
          ZAPP:{name:'Электрошок', src: require('../assets/ZAPP.png')},
          WZSP:{name:'Призрак-колдун', src: require('../assets/WZSP.png')},
          WTLK:{name:'Водная связь', src: require('../assets/WTLK.png')},
          WTEL:{name:'Элементаль воды', src: require('../assets/WTEL.png')},
          WRSP:{name:'Призрак-воин', src: require('../assets/WRSP.png')},
          WRBN:{name:'Единство', src: require('../assets/WRBN.png')},
          OST1:{name:'Бесцветный мститель', src: require('../assets/OST1.png')},
          OST2:{name:'Бесцветный каратель', src: require('../assets/OST2.png')},
          CLSP:{name:'Призрак-жнец', src: require('../assets/CLSP.png')},
          CLSN:{name:'Очищение', src: require('../assets/CLSN.png')},
          CLNP:{name:'Снятие чар', src: require('../assets/CLNP.png')},
          CLIG:{name:'Молния', src: require('../assets/CLIG.png')},
          CLFR:{name:'Маска ужаса', src: require('../assets/CLFR.png')},
          CHNL:{name:'Эфирная торговля', src: require('../assets/CHNL.png')},
          CCLP:{name:'Циклоп', src: require('../assets/CCLP.png')},
          CANV:{name:'Сила жертвоприношения', src: require('../assets/CANV.png')},
          BRRD:{name:'Огонь', src: require('../assets/BRRD.png')},
          BRMZ:{name:'Бронзовый мехозавр', src: require('../assets/BRMZ.png')},
          BRMC:{name:'Бронзовый мехос', src: require('../assets/BRMC.png')},
          BRGR:{name:'Пылающие могилы', src: require('../assets/BRGR.png')},
          BNSH:{name:'Растворение', src: require('../assets/BNSH.png')},
          BLSS:{name:'Благословение', src: require('../assets/BLSS.png')},
          BLNC:{name:'Равновесие', src: require('../assets/BLNC.png')},
          BHMO:{name:'Матка злобоглазов', src: require('../assets/BHMO.png')},
          BGVL:{name:'Большой колесник', src: require('../assets/BGVL.png')},
          BEWK:{name:'Рабочая пчела', src: require('../assets/BEWK.png')},
          BDEX:{name:'Телесный обмен', src: require('../assets/BDEX.png')},
          BATT:{name:'Летучая мышь', src: require('../assets/BATT.png')},
          AWAK:{name:'Обновление', src: require('../assets/AWAK.png')},
          AVMK:{name:'Авиак-стрелок', src: require('../assets/AVMK.png')},
          AVAS:{name:'Авиак-убийца', src: require('../assets/AVAS.png')},
          ASLT:{name:'Штурм', src: require('../assets/ASLT.png')},
          DPGS:{name:'Подавляющий газ', src: require('../assets/DPGS.png')},
          DOMN:{name:'Господство', src: require('../assets/DOMN.png')},
          DNFR:{name:'Густой лес', src: require('../assets/DNFR.png')},
          DMRT:{name:'Проклятая крыса', src: require('../assets/DMRT.png')},
          DMRD:{name:'Господство', src: require('../assets/DMRD.png')},
          DISM:{name:'Лишение силы', src: require('../assets/DISM.png')},
          DELS:{name:'Перестановка', src: require('../assets/DELS.png')},
          CTMN:{name:'Раскройщик', src: require('../assets/CTMN.png')},
          CRIV:{name:'Разрушающий плющ', src: require('../assets/CRIV.png')},
          CRHG:{name:'Проклятие голода', src: require('../assets/CRHG.png')},
          CRBL:{name:'Огненный кулак', src: require('../assets/CRBL.png')},
          COPL:{name:'Канал жизни', src: require('../assets/COPL.png')},
          COMT:{name:'Комета', src: require('../assets/COMT.png')},
          CNBL:{name:'Каннибализм', src: require('../assets/CNBL.png')},
          CNAR:{name:'Густой воздух', src: require('../assets/CNAR.png')},
          ERQA:{name:'Землетрясение', src: require('../assets/ERQA.png')},
          EREL:{name:'Элементаль земли', src: require('../assets/EREL.png')},
          ERBN:{name:'Кровные узы', src: require('../assets/ERBN.png')},
          ENRG:{name:'Подзарядка', src: require('../assets/ENRG.png')},
          ENLH:{name:'Просвещение', src: require('../assets/ENLH.png')},
          ENFR:{name:'Форсирование', src: require('../assets/ENFR.png')},
          ENFL:{name:'Бессилие', src: require('../assets/ENFL.png')},
          ENBR:{name:'Энергетический барьер', src: require('../assets/ENBR.png')},
          ENAL:{name:'Общая подзарядка', src: require('../assets/ENAL.png')},
          EHUN:{name:'Охотница', src: require('../assets/EHUN.png')},
          DWND:{name:'Истощение', src: require('../assets/DWND.png')},
          DSIG:{name:'Дезинтеграция', src: require('../assets/DSIG.png')},
          DRGS:{name:'Ослабляющий газ', src: require('../assets/DRGS.png')},
          DRFR:{name:'Темный лес', src: require('../assets/DRFR.png')},
          DRAT:{name:'Заразная крыса', src: require('../assets/DRAT.png')},
          FTCH:{name:'Эфирная почта', src: require('../assets/FTCH.png')},
          FRWN:{name:'Свежий ветер', src: require('../assets/FRWN.png')},
          FRWA:{name:'Огненная волна', src: require('../assets/FRWA.png')},
          FRGS:{name:'Веселящий газ', src: require('../assets/FRGS.png')},
          FREL:{name:'Элементаль огня', src: require('../assets/FREL.png')},
          FRBL:{name:'Огненный шар', src: require('../assets/FRBL.png')},
          FOGG:{name:'Туман', src: require('../assets/FOGG.png')},
          FMFI:{name:'Серый некромант', src: require('../assets/FMFI.png')},
          FLOD:{name:'Наводнение', src: require('../assets/FLOD.png')},
          FDBK:{name:'Зеркало', src: require('../assets/FDBK.png')},
          FAIL:{name:'Поломка', src: require('../assets/FAIL.png')},
          EXIL:{name:'Изгнание', src: require('../assets/EXIL.png')},
          ETTD:{name:'Очищающая жертва', src: require('../assets/ETTD.png')},
          ETCH:{name:'Эфирный резонанс', src: require('../assets/ETCH.png')},
          ETAM:{name:'Эфирная защита', src: require('../assets/ETAM.png')},
          GRHE:{name:'Исцеление', src: require('../assets/GRHE.png')},
          GRFG:{name:'Фингус-патриарх', src: require('../assets/GRFG.png')},
          GRDG:{name:'Зеленый дракон', src: require('../assets/GRDG.png')},
          GLDT:{name:'Слепящая пыль', src: require('../assets/GLDT.png')},
          GIFT:{name:'Подарочек', src: require('../assets/GIFT.png')},
          GIFG:{name:'Хищный фингус', src: require('../assets/GIFG.png')},
          GEYS:{name:'Гейзер', src: require('../assets/GEYS.png')},
          GEWR:{name:'Великая защита от чар', src: require('../assets/GEWR.png')},
          GCWR:{name:'Великая защита от созданий', src: require('../assets/GCWR.png')},
          GBNS:{name:'Великое растворение', src: require('../assets/GBNS.png')},
          GBIN:{name:'Гибберлин-подстрекатель', src: require('../assets/GBIN.png')},
          GBGN:{name:'Шайка гибберлинов', src: require('../assets/GBGN.png')},
          GBBR:{name:'Гибберлин', src: require('../assets/GBBR.png')},
          GBAT:{name:'Гигантская летучая мышь', src: require('../assets/GBAT.png')},
          FTDS:{name:'Роковая болезнь', src: require('../assets/FTDS.png')},
          IRMC:{name:'Железный мехос', src: require('../assets/IRMC.png')},
          IRAB:{name:'Железное исчадие', src: require('../assets/IRAB.png')},
          INCR:{name:'Искажение реальности', src: require('../assets/INCR.png')},
          IMML:{name:'Ярость', src: require('../assets/IMML.png')},
          IMMB:{name:'Парализация', src: require('../assets/IMMB.png')},
          ICST:{name:'Снежная буря', src: require('../assets/ICST.png')},
          HUNG:{name:'Голод', src: require('../assets/HUNG.png')},
          HRCN:{name:'Ураган', src: require('../assets/HRCN.png')},
          HOPR:{name:'Прыгун', src: require('../assets/HOPR.png')},
          HOAM:{name:'Враждебное окружение', src: require('../assets/HOAM.png')},
          HEAL:{name:'Лечение', src: require('../assets/HEAL.png')},
          HAST:{name:'Спешка', src: require('../assets/HAST.png')},
          GRTN:{name:'Старый древень', src: require('../assets/GRTN.png')},
          GRSN:{name:'Травяная змея', src: require('../assets/GRSN.png')},
          GROG:{name:'Серый людоед', src: require('../assets/GROG.png')},
          LMWD:{name:'Ламия-повелительница', src: require('../assets/LMWD.png')},
          LMWA:{name:'Ламия-воительница', src: require('../assets/LMWA.png')},
          LMMN:{name:'Ламия-монахиня', src: require('../assets/LMMN.png')},
          LGST:{name:'Шторм молний', src: require('../assets/LGST.png')},
          LFGR:{name:'Телохранитель', src: require('../assets/LFGR.png')},
          LEWR:{name:'Малая защита от чар', src: require('../assets/LEWR.png')},
          LCWR:{name:'Малая защита от созданий', src: require('../assets/LCWR.png')},
          LASN:{name:'Озерная змея', src: require('../assets/LASN.png')},
          KBWR:{name:'Кобольд-воин', src: require('../assets/KBWR.png')},
          KBSH:{name:'Кобольд-шаман', src: require('../assets/KBSH.png')},
          KBEL:{name:'Старейшина кобольдов', src: require('../assets/KBEL.png')},
          JYHD:{name:'Злость', src: require('../assets/JYHD.png')},
          JUST:{name:'Баланс', src: require('../assets/JUST.png')},
          JRRG:{name:'Реанимация', src: require('../assets/JRRG.png')},
          IRMZ:{name:'Железный мехозавр', src: require('../assets/IRMZ.png')},
          MGHT:{name:'Мощь', src: require('../assets/MGHT.png')},
          MDWP:{name:'Стирание разума', src: require('../assets/MDWP.png')},
          MDNS:{name:'Безумие', src: require('../assets/MDNS.png')},
          MDBL:{name:'Удар по разуму', src: require('../assets/MDBL.png')},
          MCWU:{name:'Механический червь', src: require('../assets/MCWU.png')},
          MCWO:{name:'Механический червяк', src: require('../assets/MCWO.png')},
          MALS:{name:'Проклятие', src: require('../assets/MALS.png')},
          MAHO:{name:'Магический шершень', src: require('../assets/MAHO.png')},
          LSST:{name:'Боевой дух', src: require('../assets/LSST.png')},
          LSPC:{name:'Пакет данных', src: require('../assets/LSPC.png')},
          LSHE:{name:'Оздоровление', src: require('../assets/LSHE.png')},
          LSDS:{name:'Нерешительность', src: require('../assets/LSDS.png')},
          LQRL:{name:'Кража реальности', src: require('../assets/LQRL.png')},
          LNRD:{name:'Отражение', src: require('../assets/LNRD.png')},
          LMWK:{name:'Ламия-колдунья', src: require('../assets/LMWK.png')},

          MOTI:{name:'Матка клещей', src: require('../assets/MOTI.png')},
          MOBE:{name:'Матка пчел', src: require('../assets/MOBE.png')},
          MNVR:{name:'Эфирный вихрь', src: require('../assets/MNVR.png')},
          MNTX:{name:'Эфирный урожай', src: require('../assets/MNTX.png')},
          MNTS:{name:'Мантис', src: require('../assets/MNTS.png')},
          MNTP:{name:'Эфирный родник', src: require('../assets/MNTP.png')},
          MNTH:{name:'Колючий эфир', src: require('../assets/MNTH.png')},
          MNST:{name:'Эфирный шторм', src: require('../assets/MNST.png')},
          MNHP:{name:'Малый прыгун', src: require('../assets/MNHP.png')},
          MNDW:{name:'Эфирный фонтан', src: require('../assets/MNDW.png')},
          MNDP:{name:'Капля эфира', src: require('../assets/MNDP.png')},
          MNBR:{name:'Эфирный бриз', src: require('../assets/MNBR.png')},
          MNBN:{name:'Обжигающий эфир', src: require('../assets/MNBN.png')},
          MLFI:{name:'Черный некромант', src: require('../assets/MLFI.png')},
          ORGD:{name:'Орк-стражник', src: require('../assets/ORGD.png')},
          ORFT:{name:'Бесцветное оживление', src: require('../assets/ORFT.png')},
          OREL:{name:'Старейшина орков', src: require('../assets/OREL.png')},
          OMWP:{name:'Бесцветная напасть', src: require('../assets/OMWP.png')},
          OMSW:{name:'Бесцветное воровство', src: require('../assets/OMSW.png')},
          OGKN:{name:'Вождь людоедов', src: require('../assets/OGKN.png')},
          OFLX:{name:'Бесцветный шторм', src: require('../assets/OFLX.png')},
          OEXC:{name:'Обмен планами', src: require('../assets/OEXC.png')},
          OCRP:{name:'Увечье', src: require('../assets/OCRP.png')},
          OCLV:{name:'Бесцветный молот', src: require('../assets/OCLV.png')},
          OBLS:{name:'Бесцветный удар', src: require('../assets/OBLS.png')},
          NWLF:{name:'Ночной волк', src: require('../assets/NWLF.png')},
          MYHO:{name:'Мистический шершень ', src: require('../assets/MYHO.png')},
          MXHP:{name:'Большой прыгун', src: require('../assets/MXHP.png')},
          PRBN:{name:'Эфирные узы', src: require('../assets/PRBN.png')},
          PRAT:{name:'Чумная крыса', src: require('../assets/PRAT.png')},
          PLWP:{name:'Деформация бесцветности', src: require('../assets/PLWP.png')},
          PLSL:{name:'Планарная печать', src: require('../assets/PLSL.png')},
          PLMR:{name:'Превращение', src: require('../assets/PLMR.png')},
          PHWL:{name:'Стена нереальности', src: require('../assets/PHWL.png')},
          PCDU:{name:'Пыль', src: require('../assets/PCDU.png')},
          PALN:{name:'Болевая связь', src: require('../assets/PALN.png')},
          OVRL:{name:'Перегрузка', src: require('../assets/OVRL.png')},
          OUS2:{name:'Бесцветный чародей', src: require('../assets/OUS2.png')},
          OUS1:{name:'Бесцветный маг', src: require('../assets/OUS1.png')},
          OUE2:{name:'Бесцветный ткач', src: require('../assets/OUE2.png')},
          OUE1:{name:'Бесцветный чароед', src: require('../assets/OUE1.png')},
          ORSH:{name:'Орк-шаман', src: require('../assets/ORSH.png')},
          ORPL:{name:'Бесцветная зыбь', src: require('../assets/ORPL.png')},
          RDLG:{name:'Молния', src: require('../assets/RDLG.png')},
          RDKN:{name:'Конунг', src: require('../assets/RDKN.png')},
          RDIS:{name:'Безумие', src: require('../assets/RDIS.png')},
          RDHL:{name:'Лечение', src: require('../assets/RDHL.png')},
          RDDG:{name:'Красный дракон', src: require('../assets/RDDG.png')},
          RDDF:{name:'Защита', src: require('../assets/RDDF.png')},
          RDCM:{name:'Подчинение', src: require('../assets/RDCM.png')},
          RDCL:{name:'Колосс', src: require('../assets/RDCL.png')},
          RBRT:{name:'Возрождение', src: require('../assets/RBRT.png')},
          QKFX:{name:'Быстрая починка', src: require('../assets/QKFX.png')},
          PWTP:{name:'Откачка эфира', src: require('../assets/PWTP.png')},
          PTRS:{name:'Птерос', src: require('../assets/PTRS.png')},
          PSBR:{name:'Эфирная мощь', src: require('../assets/PSBR.png')},
          PRGD:{name:'Маяк', src: require('../assets/PRGD.png')},
          PRFY:{name:'Очистка', src: require('../assets/PRFY.png')},
          SCBR:{name:'Второе рождение', src: require('../assets/SCBR.png')},
          SACR:{name:'Рассеяние', src: require('../assets/SACR.png')},
          RZMN:{name:'Потрошитель', src: require('../assets/RZMN.png')},
          RVHL:{name:'Речной нимб', src: require('../assets/RVHL.png')},
          RVHG:{name:'Опустошающий голод', src: require('../assets/RVHG.png')},
          RPST:{name:'Ремонтная мастерская', src: require('../assets/RPST.png')},
          RNST:{name:'Каменный дождь', src: require('../assets/RNST.png')},
          RKWL:{name:'Каменная стена', src: require('../assets/RKWL.png')},
          RIOT:{name:'Бескровие', src: require('../assets/RIOT.png')},
          RIFT:{name:'Разлом', src: require('../assets/RIFT.png')},
          REPR:{name:'Ремонт', src: require('../assets/REPR.png')},
          REDR:{name:'Переадресация', src: require('../assets/REDR.png')},
          RECL:{name:'Импульс данных', src: require('../assets/RECL.png')},
          RDRS:{name:'Воскрешение', src: require('../assets/RDRS.png')},
          SMVL:{name:'Малый колесник', src: require('../assets/SMVL.png')},
          SMTG:{name:'Бесцветный воин', src: require('../assets/SMTG.png')},
          SMOK:{name:'Дым', src: require('../assets/SMOK.png')},
          SMFX:{name:'Бесцветный разведчик', src: require('../assets/SMFX.png')},
          SLWL:{name:'Кремневая стена', src: require('../assets/SLWL.png')},
          SLRP:{name:'Самовосстановление', src: require('../assets/SLRP.png')},
          SHWL:{name:'Призрачный волк', src: require('../assets/SHWL.png')},
          SHSP:{name:'Призрак-шаман', src: require('../assets/SHSP.png')},
          SHSK:{name:'Ледяной панцирь', src: require('../assets/SHSK.png')},
          SHFR:{name:'Щит силы', src: require('../assets/SHFR.png')},
          SHCL:{name:'Острые когти', src: require('../assets/SHCL.png')},
          SGHT:{name:'Разрушитель чар', src: require('../assets/SGHT.png')},
          SFGS:{name:'Удушающий газ', src: require('../assets/SFGS.png')},
          SEHL:{name:'Морской нимб', src: require('../assets/SEHL.png')},
          SCSK:{name:'Ледяная чешуя', src: require('../assets/SCSK.png')},
          STMC:{name:'Стальной мехос', src: require('../assets/STMC.png')},
          STAT:{name:'Усиление атаки', src: require('../assets/STAT.png')},
          STAB:{name:'Стальное исчадие', src: require('../assets/STAB.png')},
          SPWB:{name:'Паутина душ', src: require('../assets/SPWB.png')},
          SPTR:{name:'Эфирная жажда', src: require('../assets/SPTR.png')},
          SPRG:{name:'Уничтожение', src: require('../assets/SPRG.png')},
          SPLK:{name:'Эфирная подпитка', src: require('../assets/SPLK.png')},
          SPFG:{name:'Плюющийся фингус', src: require('../assets/SPFG.png')},
          SPBR:{name:'Очищение', src: require('../assets/SPBR.png')},
          SPBA:{name:'Призрак василиска', src: require('../assets/SPBA.png')},
          SNRG:{name:'Кольцо камней', src: require('../assets/SNRG.png')},
          SNOV:{name:'Сверхновая', src: require('../assets/SNOV.png')},
          SNHG:{name:'Сад камней', src: require('../assets/SNHG.png')},
          SNCR:{name:'Круг камней', src: require('../assets/SNCR.png')},
          SMWB:{name:'Бесцветный рыцарь', src: require('../assets/SMWB.png')},
          TIWR:{name:'Клещ-воин', src: require('../assets/TIWR.png')},
          TIWK:{name:'Рабочий клещ', src: require('../assets/TIWK.png')},
          TIQN:{name:'Королева клещей', src: require('../assets/TIQN.png')},
          THIF:{name:'Вор', src: require('../assets/THIF.png')},
          TGSK:{name:'Ледяной щит', src: require('../assets/TGSK.png')},
          TANT:{name:'Древень', src: require('../assets/TANT.png')},
          SYMB:{name:'Симбиоз', src: require('../assets/SYMB.png')},
          SWSN:{name:'Болотная змея', src: require('../assets/SWSN.png')},
          SWMN:{name:'Пильщик', src: require('../assets/SWMN.png')},
          SWHL:{name:'Болотный нимб', src: require('../assets/SWHL.png')},
          STWO:{name:'Сила леса', src: require('../assets/STWO.png')},
          STSK:{name:'Каменная кожа', src: require('../assets/STSK.png')},
          STSI:{name:'Стазис', src: require('../assets/STSI.png')},
          STRT:{name:'Вонючая крыса', src: require('../assets/STRT.png')},
          STRG:{name:'Усиление', src: require('../assets/STRG.png')},
          WHAS:{name:'Клубящийся пепел', src: require('../assets/WHAS.png')},
          WETX:{name:'Слабый токсин', src: require('../assets/WETX.png')},
          WEAK:{name:'Слабость', src: require('../assets/WEAK.png')},
          VBAT:{name:'Летучая мышь-вампир', src: require('../assets/VBAT.png')},
          UPGD:{name:'Благословение леса', src: require('../assets/UPGD.png')},
          UNSM:{name:'Развоплощение', src: require('../assets/UNSM.png')},
          TWEN:{name:'Искаженное улучшение', src: require('../assets/TWEN.png')},
          TRSN:{name:'Древесная змея', src: require('../assets/TRSN.png')},
          TRLB:{name:'Притяжение данных', src: require('../assets/TRLB.png')},
          TREM:{name:'Дрожь', src: require('../assets/TREM.png')},
          TRAD:{name:'Причудливый обмен', src: require('../assets/TRAD.png')},
          TPLF:{name:'Откачака жизни', src: require('../assets/TPLF.png')},
          TNSA:{name:'Молодой древень', src: require('../assets/TNSA.png')},
          ORWR:{name:'Орк-воин', src: require('../assets/ORWR.png')},
          PRWR:{name:'Планарный барьер', src: require('../assets/PRWR.png')},
          SPWR:{name:'Дух войны', src: require('../assets/SPWR.png')},
          PRWT:{name:'Обесцвечивание', src: require('../assets/PRWT.png')},
          STMZ:{name:'Стальной махозавр', src: require('../assets/STMZ.png')},
          VTLY:{name:'Живучесть', src: require('../assets/VTLY.png')},
          WLDT:{name:'Стена искажений', src: require('../assets/WLDT.png')},
          WLDG:{name:'Стена кинжалов', src: require('../assets/WLDG.png')},
          TWST:{name:'Смерч', src: require('../assets/TWST.png')},
          STTX:{name:'Сильный токсин', src: require('../assets/STTX.png')},
          WLBZ:{name:'Стаена бриза', src: require('../assets/WLBZ.png')},
          WLBR:{name:'Стена горящего камня', src: require('../assets/WLBR.png')},
          WLBL:{name:'Стена лезвий', src: require('../assets/WLBL.png')},
          WLAR:{name:'Стена воздуха', src: require('../assets/WLAR.png')},
          WKHR:{name:'Ходячий ужас', src: require('../assets/WKHR.png')},
          FRNT:{name:'Ярость природы', src: require('../assets/FRNT.png')},
          ASST:{name:'Пепельный шторм', src: require('../assets/ASST.png')},
          GRUN:{name:'Великое развопрощение', src: require('../assets/GRUN.png')},
          CURS:{name:'Напасть', src: require('../assets/CURS.png')},
          CTST:{name:'Катастрофа', src: require('../assets/CTST.png')},
          FRST:{name:'Огненный шторм', src: require('../assets/FRST.png')},
          MCWY:{name:'Червезавр', src: require('../assets/MCWY.png')},
          IRRT:{name:'Раздражение', src: require('../assets/IRRT.png')},
          GSWR:{name:'Великая защита от колдовства', src: require('../assets/GSWR.png')},
          WLFC:{name:'Стена силы', src: require('../assets/WLFC.png')},
          MTPT:{name:'Мантис-патриарх', src: require('../assets/MTPT.png')},
          FURY:{name:'Бешенство', src: require('../assets/FURY.png')},
          LSUS:{name:'Малое развоплощение', src: require('../assets/LSUS.png')},
          LSWR:{name:'Малая защита от колдовства', src: require('../assets/LSWR.png')},
          ANFR:{name:'Древний лес', src: require('../assets/ANFR.png')},
          AVEL:{name:'Старейшина авиаков', src: require('../assets/AVEL.png')},
          CLON:{name:'Клонирование', src: require('../assets/CLON.png')},
          AVSC:{name:'Авиак-разведчик', src: require('../assets/AVSC.png')},
          ALNS:{name:'Клеймо бесцветности', src: require('../assets/ALNS.png')},
          AMNS:{name:'Амнезия', src: require('../assets/AMNS.png')},
          BEWR:{name:'Пчела-воин', src: require('../assets/BEWR.png')},
          ANTN:{name:'Вековой древень', src: require('../assets/ANTN.png')},
          BRRL:{name:'Разрушение реальности', src: require('../assets/BRRL.png')},
          BHWR:{name:'Злобоглаз-воин', src: require('../assets/BHWR.png')},
          ALST:{name:'Альфа-удар', src: require('../assets/ALST.png')},
          ENVM:{name:'Отравление', src: require('../assets/ENVM.png')},
          BROT:{name:'Сожжение', src: require('../assets/BROT.png')},
          ENWO:{name:'Стойкость леса', src: require('../assets/ENWO.png')},
          KBGD:{name:'Кобольд-стражник', src: require('../assets/KBGD.png')},
          BLDG:{name:'Черный дракон', src: require('../assets/BLDG.png')},
          DECN:{name:'Разочарование', src: require('../assets/DECN.png')},
          CHCM:{name:'Переподчинение', src: require('../assets/CHCM.png')},
          BLKD:{name:'Защита', src: require('../assets/BLKD.png')},
          FLIC:{name:'Язык пламени', src: require('../assets/FLIC.png')},
          BANN:{name:'Склероз', src: require('../assets/BANN.png')},
          CMBN:{name:'Самопожертвование', src: require('../assets/CMBN.png')},
          AILK:{name:'Воздушная связь', src: require('../assets/AILK.png')},
          BUDG:{name:'Синий дракон', src: require('../assets/BUDG.png')},
          ANHL:{name:'Аннигиляция', src: require('../assets/ANHL.png')},
          AREL:{name:'Элементаль воздуха', src: require('../assets/AREL.png')},
          BASO:{name:'Шипы душ', src: require('../assets/BASO.png')},
          BEQN:{name:'Королева пчел', src: require('../assets/BEQN.png')},
          OST3:{name:'Бесцветный искупитель', src: require('../assets/OST3.png')},
          OUS3:{name:'Бесцветный целитель', src: require('../assets/OUS3.png')},
          WNPZ:{name:'Парализация', src: require('../assets/WNPZ.png')},
          WNMG:{name:'Маггот', src: require('../assets/WNMG.png')},
          WNHR:{name:'Охотница', src: require('../assets/WNHR.png')},
          WNFL:{name:'Огненный ветер', src: require('../assets/WNFL.png')},
          WNBL:{name:'Благословение', src: require('../assets/WNBL.png')},
          WLWD:{name:'Стена ветра', src: require('../assets/WLWD.png')},
          CAFG:{name:'Плотоядный фингус', src: require('../assets/CAFG.png')},
          WLMG:{name:'Стена магмы', src: require('../assets/WLMG.png')},
          BEHD:{name:'Злобоглаз', src: require('../assets/BEHD.png')},
          WLLV:{name:'Стена лавы', src: require('../assets/WLLV.png')},
          WLIL:{name:'Стена иллюзии', src: require('../assets/WLIL.png')},
          WLIF:{name:'Стена адского пламени', src: require('../assets/WLIF.png')},
          BRAB:{name:'Бронзовое исчадие', src: require('../assets/BRAB.png')},
          MTSC:{name:'Мантус-летописец', src: require('../assets/MTSC.png')},
          NOIS:{name:'Помехи', src: require('../assets/NOIS.png')},
          RFPT:{name:'Армированный птерос', src: require('../assets/RFPT.png')},
          VOLC:{name:'Вулкан', src: require('../assets/VOLC.png')},
        }
      },
      openReplaceModal(mode, index) {
        this.$refs['my-modal-1'].show()
        this.locationMode = mode
        this.locationIndex = index
      },
      async parsXml() {
        let text = await this.file1.text()
        let parser = new DOMParser();
        this.xmlDoc = parser.parseFromString(text,"text/xml");
        this.deck = this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("deck")
      },
      async changeXml(key) {
        let i = this.locationIndex
        let temp = this.xmlDoc
        if (this.locationMode == 'deck') {
          this.xmlDoc = null
          this.xmlDoc = temp
          this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("deck")[0].children[i].attributes[0].value = key
          this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("deck")[0].children[i].attributes[0].textcontent = key
        }
        else if (this.locationMode == 'bag') {
          this.xmlDoc = null
          this.xmlDoc = temp
          this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("bag")[0].children[i].attributes[0].value = key
          this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("bag")[0].children[i].attributes[0].textcontent = key
        }

        this.$refs['my-modal-1'].hide()
        this.locationMode = null
        this.locationIndex = null
      },
      async saveXml() {
        var xmlText = new XMLSerializer().serializeToString(this.xmlDoc);
        // console.log("xml", xml)
        if (xmlText) {
          const type="text/plain"
          var url = window.URL.createObjectURL(new Blob([xmlText], { type }));
          var a = document.createElement('a');
          a.href = url;
          a.download = `edited_savegame.savc`;
          document.body.appendChild(a); // we need to append the element to the dom -> otherwise it will not work in firefox
          a.click();
          a.remove();  //afterwards we remove the element again
        }
      }
    },
    computed: {
      deckSpells() {
        let list = []
        if (this.xmlDoc) {
          for (let i=0; i<this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("deck")[0].children.length; i++) {
            list.push({
              value: this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("deck")[0].children[i].attributes[0].value,
              index: i
            })
          }
        }
        return list
      },
      bagSpells() {
        let list = []
        if (this.xmlDoc) {
          for (let i=0; i<this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("bag")[0].children.length; i++) {
            if (this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("bag")[0].children[i].tagName == 'spell') {
              list.push({
                value: this.xmlDoc.getElementsByTagName("players")[0].getElementsByTagName("heroes")[0].getElementsByTagName("bag")[0].children[i].attributes[0].value,
                index: i
              })
            }
          }
        }
        return list
      },
      filteredDB() {
        const asArray = Object.entries(this.dataBase);
        const filtered = asArray.filter(([key, value]) => this.filterName? value.name.toLowerCase().includes(this.filterName.toLowerCase()) : true);
        const fdb = Object.fromEntries(filtered);
        return fdb
      },
      filteredDBList() {
        const asArray = Object.entries(this.dataBase);
        const filtered = asArray.filter(([key, value]) => this.filterNameList? value.name.toLowerCase().includes(this.filterNameList.toLowerCase()) : true);
        const fdb = Object.fromEntries(filtered);
        return fdb
      },
    },
    created() {
      this.getDB()
    }
  }

</script>


<style>
table {
  font-family: arial, sans-serif;
  border-collapse: collapse;
  /* width: 90%; */
}

td, th {
  border: 1px solid #dddddd;
  text-align: left;
  padding: 8px;

}
/* 
tr:nth-child(even) {
  background-color: #dddddd;
} */

 .card-title{
    font-size:16px;
}
 .card-text{
    font-size:10px;
    word-wrap: normal;
    overflow-wrap: normal;
}
.icon {
  height: 75px;
  width: 75px;
  vertical-align: middle;
  cursor: pointer;
}

</style>