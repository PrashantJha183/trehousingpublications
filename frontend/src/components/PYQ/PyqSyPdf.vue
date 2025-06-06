<template>
  <div v-if="isLoading" class="loading-container">
    <div class="loading-spinner"></div>
    <p>Loading content, please wait...</p>
  </div>

  <div v-else class="box">
    <div class="heading-pdf">
      <h4>BPSC TRE Question Papers</h4>
    </div>

    <div v-if="pdfData['BPSC TRE']">
      <div
        v-for="(papers, category) in pdfData['BPSC TRE']"
        :key="category"
        class="category-section"
      >
        <h3 class="table-head">
          <u class="underline">{{ category }}</u>
        </h3>
        <br />

        <table class="table-data">
          <thead>
            <tr class="imp-link">
              <th>PDF File Name</th>
              <th>Download</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(file, index) in papers" :key="index" class="rowData">
              <td>{{ file }}</td>
              <td>
                <a
                  :href="getPdfUrl(file)"
                  target="_blank"
                  rel="noopener noreferrer"
                  >View</a
                >
              </td>
            </tr>
          </tbody>
        </table>
        <br /><br />
      </div>
    </div>

    <div v-else>
      <p>No data available for this selection.</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "PyqSyPdf",
  data() {
    return {
      pdfData: {},
      isLoading: true,
      selectedCategory: null,
      courseSubjectMap: [], // will hold data from /api/v1/
    };
  },
  watch: {
    "$route.query": {
      handler: "fetchAllData",
      immediate: true,
    },
  },
  methods: {
    async fetchAllData() {
      const courseId = this.$route.query.course_id;
      const subjectId = this.$route.query.subject_id;

      if (!courseId || !subjectId) {
        this.pdfData = {};
        this.selectedCategory = null;
        return;
      }

      this.isLoading = true;

      try {
        // Step 1: Fetch course/subject mapping from /api/v1/
        const mapRes = await axios.get(
          `https://cms.trehousingpublication.com/api/v1/`
        );
        this.courseSubjectMap = mapRes.data || [];

        // Step 2: Find subject title dynamically from courseSubjectMap
        const selectedCourse = this.courseSubjectMap.find(
          (course) => course.id == courseId
        );
        if (selectedCourse) {
          const selectedSubject = selectedCourse.subjects.find(
            (subject) => subject.id == subjectId
          );
          this.selectedCategory = selectedSubject
            ? selectedSubject.title
            : null;
        } else {
          this.selectedCategory = null;
        }

        // Step 3: Fetch PDF data
        const dataUrl = `https://cms.trehousingpublication.com/api/v2/?course_id=${courseId}&subject_id=${subjectId}`;
        const dataRes = await axios.get(dataUrl);
        this.pdfData = dataRes.data || {};
      } catch (error) {
        console.error("Error fetching data:", error);
        this.pdfData = {};
        this.selectedCategory = null;
      } finally {
        this.isLoading = false;
      }
    },

    getPdfUrl(filename) {
      const courseId = this.$route.query.course_id;
      const subjectId = this.$route.query.subject_id;
      return `https://cms.trehousingpublication.com/api/v2/?course_id=${courseId}&subject_id=${subjectId}&file=${encodeURIComponent(
        filename
      )}`;
    },
  },
};
</script>

<style scoped>
/* Loading styles */
.loading-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 200px;
  width: 100%;
}

.loading-spinner {
  border: 4px solid rgba(0, 0, 0, 0.1);
  border-radius: 50%;
  border-top: 4px solid #3498db;
  width: 40px;
  height: 40px;
  animation: spin 1s linear infinite;
  margin-bottom: 15px;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

/* No data message styles */
.no-data-message {
  padding: 15px;
  background-color: #f8f9fa;
  border-radius: 5px;
  text-align: center;
  margin: 10px 0;
  color: #6c757d;
}

/* Your existing styles can remain here */

.box {
  padding: 3vh 3vw;
  font-family: "Inter", sans-serif;
}

.heading-pdf {
  padding: 0px;
  border-radius: 5px;
  font-weight: bolder;
}
.underline {
  color: rgb(208, 5, 35);
}

.heading-pdf h4 {
  font-size: 24px;
  margin: 0;
  padding: 0 0px;
  font-family: "Times New Roman", Times, serif;
  font-weight: bold;
}

.paragraph-pdf {
  font-size: medium;
  color: #000;
  margin: 14px 0;
}

.table-head {
  margin-bottom: -10px;
  font-size: medium;
  font-weight: bold;
  color: rgb(208, 5, 35);
  text-align: center;
}

/* .horiz-line {
    width: 50vw;
    border-color: #ef5446;
    margin-bottom: 15px;
} */

.table-data {
  width: 100%;
  border-collapse: collapse;
  margin: 25px 0px;
}

.table-data .imp-link th {
  color: #fff;
  background-color: #d6574b;
  font-weight: 700;
  padding: 15px;
  font-size: medium;
  text-align: center;
}

.table-data .rowData {
  border: 1.5px solid #000;
}

.table-data .rowData td {
  text-decoration: none;
  border: 1px solid #000;
  font-size: medium;
  font-weight: 550;
  padding: 1.3vw;
  color: rgb(208, 5, 35);
  text-align: center;
}

/* Responsive Design */
@media (max-width: 1024px) {
  .paragraph-pdf {
    font-size: 17px;
  }

  .table-head {
    font-size: 16px;
  }

  .table-data .imp-link th {
    font-size: 1rem;
  }

  .table-data .rowData td {
    font-size: 15px;
  }
}

@media (max-width: 768px) {
  .box {
    padding: 1vh 4vw;
  }

  .heading-pdf h4 {
    font-size: 20px;
  }

  .paragraph-pdf {
    font-size: 14px;
  }

  .table-head {
    font-size: 1rem;
    font-weight: 700;
  }

  .table-data .imp-link th {
    font-size: 14px;
  }

  .table-data .rowData td {
    font-size: 14px;
    padding: 15px;
  }
}

@media (max-width: 480px) {
  .heading-pdf {
    width: 100%;
  }

  .heading-pdf h4 {
    font-size: 16px;
  }

  .paragraph-pdf {
    font-size: 13px;
    line-height: 1.3;
  }

  .table-head {
    font-size: 14px;
  }

  .horiz-line {
    width: 80vw;
    margin-bottom: 15px;
  }

  .table-data .imp-link th {
    font-size: 12px;
    padding: 5px;
  }

  .table-data .rowData td {
    font-size: 11px;
    padding: 10px;
  }

  .table-data .rowData {
    border: 1px solid #000;
  }

  .table-data .rowData td {
    border: 0.7px solid #000;
  }
}
</style>
