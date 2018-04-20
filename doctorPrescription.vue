<template>
  <div v-if="consultationObj">
    <h2 class="heading-title">{{$t("message.doctorPMS.doctorPrescription.title")}}</h2>
    <div class="content-section recp-vitals">
      <div class="user-wrapper-block">
       <div class="image-block">
<!--           <div class="inner-image-block">
           <figure><img src="http://via.placeholder.com/150x150" alt="Profile Pic"></figure>
          </div> -->
          <div class="profile-sidebox">
           <div class="profileTable">
             <div class="profileTableCell fcell"><strong>Patient Name</strong></div>
             <div class="profileTableCell">: <strong style="color: #8e5ba6;">{{consultationObj.patientInfo.patientFirstName}} {{consultationObj.patientInfo.patientLastName}}</strong></div>
           </div>
           <div class="profileTable">
             <div class="profileTableCell fcell"><strong>Zoylo Id</strong></div>
             <div class="profileTableCell">:  {{consultationObj.patientInfo.patientZoyloId}}</div>
           </div>
           <div class="profileTable">
             <div class="profileTableCell fcell"><strong>Age</strong></div>
             <div class="profileTableCell">: {{consultationObj.patientInfo.patientAge}} Years</div>
           </div>
           <div class="profileTable">
             <div class="profileTableCell fcell"><strong>Gender</strong></div>
             <div class="profileTableCell">: {{consultationObj.patientInfo.patientGender}}</div>
           </div>
           <div class="profileTable">
             <div class="profileTableCell fcell"><strong>Doctor Name</strong></div>
             <div class="profileTableCell">: <strong style="color: #8e5ba6;">Dr.{{consultationObj.serviceResourceInfo.serviceResourceFirstName}} {{consultationObj.serviceResourceInfo.serviceResouceLastName}}</strong> </div>
           </div>
          </div>
        </div>
          <div class="summary-block">
           <p style="font-size:15px;font-weight:bold;">Summary</p>
           <div class="tabsSummary">
           <div v-for='(tab, index) in theSummary' :key=index>
             <span v-if="tab.value.length !== 0">
             <h3>{{ tab.key }}</h3>
             <p>
               <span v-for='(oneRow, index) in tab.value' :key=index v-if="typeof tab.value === 'object'"><i class="fa fa-chevron-right"></i> {{ oneRow }}</span>
               <span v-if="typeof tab.value === 'string'"><i class="fa fa-chevron-right"></i> {{ tab.value }}</span>
            </p>
            </span>
           </div>
           </div>
           <!-- <textarea v-model="theSummary" readonly rows="10" cols="100"></textarea> -->
          </div>
     </div>
    </div> <br>
 <div class="flp-block">
  <p><span>Follow Up</span> <span>Generate e-Prescription</span></p>
 </div>
    <div class="clinic-main-wrapper">
      <!-- Clinic box starts here-->
      <div class="clinic-wrapper">
        <!-- Clinic tab link starts here-->
        <div class="clinic-tab-link">
          <ul class="prescriptionTabs" style="min-height:27px;">
            <li :class="activeVitalClass"><a href="javascript:void(0);" @click="changeRoute('vitals')">VITALs</a></li>
            <li :class="activeComplaintsClass"><a href="javascript:void(0);" @click="changeRoute('complaints')">COMPLAINTS</a></li>
            <li :class="activeDiseasesClass"><a href="javascript:void(0);" @click="changeRoute('diseases')">DISEASES</a></li>
            <li :class="activeInvestigation"><a href="javascript:void(0);" @click="changeRoute('investigation')">INVESTIGATION</a></li>
            <li :class="activeMedicationClass"><a href="javascript:void(0);" @click="changeRoute('medication')">MEDICATION</a></li>
<!--             <li :class="activeProceduresClass"><a href="javascript:void(0);" @click="changeRoute('procedures')">PROCEDURES</a></li> -->
            <li :class="activeNotesClass"><a href="javascript:void(0);" @click="changeRoute('notes')">NOTES</a></li>
          </ul>
        </div>
        <!-- Clinic tab link end here-->
        <!-- Clinic tab content starts here-->
        <div class="clinic-tab-content">
           <router-view></router-view>
        </div>
        <!-- Clinic tab content starts here-->
      </div>
      <!-- Clinic box ends here-->
      <div class="clearBoth"></div>
    </div>
</div>  
</template>
<script>
  import ePrescriptionServices from '../services/doctorEPrescriptionServices';
  import vitals from './vitals';

  export default {
    components: {
      vitals,
    },
    data() {
      return {
        text: 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore',
        activeVitalClass: 'active',
        activeComplaintsClass: '',
        activeDiseasesClass: '',
        activeInvestigation: '',
        activeMedicationClass: '',
        activeProceduresClass: '',
        activeNotesClass: '',
        consultationObj: '',
        doctorfullName: '',
        theSummary: [
          {
            key: 'Vitals',
            value: [],
          },
          {
            key: 'Complaints',
            value: [],
          },
          {
            key: 'Diseases',
            value: [],
          },
          {
            key: 'Investigation',
            value: [],
          },
          {
            key: 'Medication',
            value: [],
          },
          {
            key: 'Procedures',
            value: [],
          },
          {
            key: 'Notes',
            value: '',
          },
        ],
        vitalsInfoList: [],
        complaintInfoList: [],
        diagnosisInfoList: [],
        investigationInfoList: [],
        medicationInfoList: [],
        complaintNotes: '',
      };
    },
    methods: {
      changeRoute(routename) {
        this.$router.push({ name: routename });
      },
      getDoctorConsultionDetails() {
        const consultationId = this.$route.params.id;
        ePrescriptionServices.getZoyloDoctorConsultationById(this.$http, consultationId).then((response) => { // eslint-disable-line
          if (response.status === 200) {
            this.consultationObj = response.data.data;
            console.log('sss', this.consultationObj);
          }
        });
      },
      processSummaryDisplay() {
        function prepareValue(theArray, ObjPropertiesToMap) { // eslint-disable-line
          const value = theArray.map(function(elem) { // eslint-disable-line
            let information = '';
            for (let i = 0; i < ObjPropertiesToMap.length; i += 1) {
              if (i !== ObjPropertiesToMap.length - 1) { // eslint-disable-line
                information += `${elem[ObjPropertiesToMap[i]]}, `;
              } else {
                information += `${elem[ObjPropertiesToMap[i]]}`;
              }
            }
            return information;
          }).join(';');
          const finalValue = value.split(';');
          return finalValue;
        }
        /** Uncomment this block and correct the lines,
         once vitalsData included to 'consultationObj' responce */
        // if (this.consultationObj.vitalsList !== null ||
        // typeof this.consultationObj.vitalsList !== 'undefined') {
        //   this.$set(this.theSummary[0], 'value',
        // prepareValue(this.consultationObj.vitalsList, ['']));
        // }
        if (this.complaintInfoList && this.complaintInfoList.length > 0) {
          this.$set(this.theSummary[1], 'value', prepareValue(this.complaintInfoList, ['complaintText', 'problemSinceValue', 'problemSinceUnitCode', 'problemSeverityCode']));
        }
        if (this.diagnosisInfoList && this.diagnosisInfoList.length > 0) {
          this.$set(this.theSummary[2], 'value', prepareValue(this.diagnosisInfoList, ['diagnosisText', 'natureOfDiagnosisText']));
        }
        if (this.investigationInfoList && this.investigationInfoList.length > 0 && typeof this.investigationInfoList !== 'undefined') {
          this.$set(this.theSummary[3], 'value', prepareValue(this.investigationInfoList, ['investigationInfoText']));
        }
        if (this.medicationInfoList && this.medicationInfoList.length > 0 && typeof this.medicationInfoList !== 'undefined') {
          this.$set(this.theSummary[4], 'value', prepareValue(this.medicationInfoList, ['formulationName', 'brandName', 'dosageText', 'frequencyTypeCode']));
        }
        if (this.complaintNotes && this.complaintNotes !== '') {
          this.$set(this.theSummary[6], 'value', this.complaintNotes);
        }
      },
    },
    mounted() {
      this.changeRoute('vitals');
      this.getDoctorConsultionDetails();
      console.log('this', this.theSummary);
      this.eventHub.$on('vitalsInfoList', (data) => {
        this.vitalsInfoList = data;
        const value = this.vitalsInfoList.map(function(elem) { // eslint-disable-line
          return elem.healthRecordKeyValue;
        }).join(', ');
        this.$set(this.theSummary[0], 'value', value);
        console.log('ssou', value, this.theSummary);
      });
      this.eventHub.$on('complaintInfoList', (data) => {
        this.complaintInfoList = data;
        this.processSummaryDisplay();
      });
      this.eventHub.$on('diagnosisInfoList', (data) => {
        this.diagnosisInfoList = data;
        this.processSummaryDisplay();
      });
      this.eventHub.$on('investigationInfoList', (data) => {
        this.investigationInfoList = data;
        this.processSummaryDisplay();
      });
      this.eventHub.$on('medicationInfoList', (data) => {
        this.medicationInfoList = data;
        this.processSummaryDisplay();
      });
      this.eventHub.$on('complaintNotes', (data) => {
        this.complaintNotes = data;
        console.log('complaintNotes', this.complaintNotes);
        this.processSummaryDisplay();
      });
    },
    // mounted() {
    //   alert('1');
    //   this.eventHub.$on('IsSummaryUpdated', (ans) => {
    //     alert('2');
    //     console.log('ans', ans);
    //   });
    // },
};
</script>

<style>
.clearBoth{
  clear: both;
}
.clinic-main-wrapper{
  background: #fff;
  padding: 10px;
}
.clinic-main-wrapper .prescriptionTabs li{
  float: left;
}
.clinic-main-wrapper .prescriptionTabs li a{
  color: #6d6e70 !important;
  font-weight: bold;
    padding: 10px 25px;
  border-right: 1px solid #ccc;
  }
.clinic-main-wrapper .prescriptionTabs li.active a {
  background: #8f55a3 !important;
  color: #fff !important;
}
 .recp-vitals {
    background: #fff;
    border-radius: 5px;
    margin-top: 28px;
    padding: 10px;
}
.recp-vitals .image-block{
 display: table-cell;
 vertical-align: top;
 height: 248px;
 border-right: 1px solid #ddd;
 padding-top: 20px;
 padding-right: 10px;
 width: 40%;
}
.recp-vitals .inner-image-block{
  display: table-cell;
  vertical-align: top;
}
.recp-vitals .image-block .profile-sidebox{
  display: table-cell;
  vertical-align: top;
  padding-left: 15px;
}
.recp-vitals .summary-block{
  display: table-cell;
  vertical-align: top;
  padding-left: 25px;
  padding-top: 20px
}
.recp-vitals .inner-image-block img{
  width: 110px;
  border-radius: 50%;
}
.recp-vitals .summary-block textarea, .recp-vitals .notes-block textarea{
  border: 1px solid #ddd;
}
.recp-vitals .profile-sidebox h2{
  color: #333;
  font-size: 18px;
}
.recp-vitals .profile-sidebox h4{
  font-size: 14px;
  color: #333;
}
.tabs-list li{
  float: left;
  border-right: 1px solid #ddd;
  padding: 5px 18px;
  font-size: 16px;
}
.tabs-list li:last-child{
  border-right: 0px;
}
.tabs-list li a{
  color: #636363;
  text-transform: uppercase;
}
.tabs-list li.active a{
  color: #8c579f;
  text-decoration: underline;
}
.tabs-block{
 margin-top: 35px;
 margin-bottom: 20px;
}
.clear-fix{
  clear: both;
}
 /* */
  .clearBoth {
    clear: both;
  }
  .addNewRec a{
    color: rgb(11, 122, 221);
    text-decoration: none !important;
    font-weight: 600;
    font-size: 13px;
  }
  .deleteBtn{
    color: red;
    margin: 30px 0 0 20px;
  }
.tabs-contentWrap label{
color: #739ccd;
    text-transform: uppercase;
    font-family: san_francisco_displaymedium,sans-serif;
    font-size: 15px;
    display: block;
    margin-bottom: 5px;
}
.tabs-contentWrap li{
    margin: 0 1% 30px 0;
    float:left;
}
.tabs-contentWrap li select{
  font-family: san_francisco_textregular,sans-serif;
    font-size: 13px;
    line-height: 37px;
    height: 37px;
    width: 260px;
    color: #9b9b9b;
    border-color: #ddd;
    padding: 0 10px;
    -webkit-appearance: menulist;
    /*background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAoAAAAHCAYAAAFGq+z1AAAAGXRFWâ€¦4gngfEjUCcDbVvGlRxIhAvgFmzAGpVHVTBdCibCSrHAADekykQu305ygAAAABJRU5ErkJggg==');
    background-repeat: no-repeat;
    background-position: 97%;*/
}
.flp-block {
  border-top: 1px solid #ddd;
  border-bottom: 1px solid #ddd;
  margin-top: 20px;
  clear: both;
}
.flp-block p{
  text-align: right;
  padding-top: 15px;
  font-size: 16px;
  color: #0b7add ;
  padding-bottom: 15px;
}
.flp-block span{
  padding-right: 10px;
}
.checkbox-wrap {
    display: block;
    position: relative;
    padding-left: 30px;
    margin-bottom: 12px;
    cursor: pointer;
    font-size: 22px;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
/* Create a custom checkbox */
.checkmark {
    position: absolute;
    top: -3px;
    left: 0;
    height: 22px;
    width: 22px;
    background-color: #fff;
    border: 1px solid #ddd;
    border-radius: 2px;
}

/* When the checkbox is checked, add a blue background */
.checkbox-wrap input:checked ~ .checkmark {
    background-color: #2196F3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
    content: "";
    position: absolute;
    display: none;
}

/* Show the checkmark when checked */
.checkbox-wrap  input:checked ~ .checkmark:after {
    display: block;
}

/* Style the checkmark/indicator */
.checkbox-wrap .checkmark:after {
    left: 7px;
    top: 3px;
    width: 5px;
    height: 10px;
    border: solid white;
    border-width: 0 3px 3px 0;
    -webkit-transform: rotate(45deg);
    -ms-transform: rotate(45deg);
    transform: rotate(45deg);
}
.medication-block label.checkbox-wrap{
  color: #636363;
  text-transform: capitalize;
  margin-bottom: 20px;
}
.dlt-btn{
  text-align:right;
  /*border-bottom:1px solid #ddd;*/
  margin-bottom:20px;
  color:red;
  text-decoration: underline;
  line-height: 10px;
}
 
  .clearBoth {
    clear: both;
  }
  .addNewRec a{
    color: #fff !important;
    background-color: rgb(41, 170, 23);
    text-decoration: none !important;
    font-weight: 600;
    font-size: 13px;
    padding: 10px 8px;
    border-radius: 4px;
  }
  .deleteBtn{
    color: red;
    margin: 30px 0 0 20px;
  }
  .deleteBtn a{
     color: #fff;
    background-color: red;
    padding: 4px 8px;
    text-transform: capitalize;
    border-radius: 3px;
    opacity: 0.8;
  }
  .profileTable {
    display: table;
    width: 100%;
    table-layout: fixed; 
    vertical-align: middle;
  }
  .profileTable .fcell{
    width: 100px;
  }
  .profileTableCell {
    display: table-cell;
  }
  .clinic-tab-link{
    margin-bottom: 30px;
    border-bottom: 1px solid #dcd8d8;
  }
  .tabsSummary {
    height:250px !important;overflow-y:scroll;border:2px solid #f2f2f2;padding:10px;
  }
  .tabsSummary h3{
    font-size: 14px;
    font-weight: bold;
  }
  .tabsSummary p{
    border-bottom: 1px solid #ddd;
  }
  .tabsSummary p i{
    margin:0 2px 0 5px;
  }
  .tabsSummary p span{
   display: block;
  }
@media(max-width:767px){
  .doctor-content-section.clinic .doctor-content-container .fees-section .clinic-main-wrapper .clinic-wrapper .clinic-tab-link ul li{
    width: 100%;
  }
  .doctor-content-section.clinic .doctor-content-container .fees-section .clinic-main-wrapper .clinic-wrapper .clinic-tab-content .about-clinic-content ul li{
    width: 100%;
  }
}
</style>
