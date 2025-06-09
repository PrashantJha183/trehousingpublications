<template>
  <div v-if="isLoading" class="loading-container">
    <div class="loading-spinner"></div>
    <p>Loading content, please wait...</p>
  </div>

  <div v-else class="box">
    <div class="heading-pdf">
      <h4>BPSC TRE Question Papers</h4>
    </div>

    <div v-if="pdfData['BPSC TRE'] && Object.keys(pdfData['BPSC TRE']).length">
      <div
        v-for="(versionData, versionKey) in pdfData['BPSC TRE']"
        :key="versionKey"
        class="version-section"
      >
        <h3 class="version-title text-center mt-2">{{ versionKey }}</h3>

        <div
          v-for="(categoryFiles, categoryName) in versionData"
          :key="categoryName"
          class="category-section"
        >
          <h4 class="category-title">
            <u class="underline">{{ categoryName }}</u>
          </h4>

          <table class="table-data">
            <thead>
              <tr class="imp-link">
                <th>PDF File Name</th>
                <th>View</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(file, idx) in categoryFiles"
                :key="idx"
                class="rowData"
              >
                <td style="text-align: justify">{{ file }}</td>
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
          <br />
        </div>
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
      courseSubjectMap: [],
      currentSubCourseId: null,
      currentSubjectId: null, // correct subject.id (e.g., 14,15,16...)
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
      const subCourseId = this.$route.query.sub_courses;

      if (!courseId || !subCourseId) {
        this.pdfData = {};
        this.selectedCategory = null;
        this.currentSubCourseId = null;
        this.currentSubjectId = null;
        return;
      }

      this.isLoading = true;

      try {
        // Step 1: Fetch course-subject mapping
        const mapRes = await axios.get(
          "https://cms.trehousingpublication.com/api/v1/"
        );
        this.courseSubjectMap = mapRes.data || [];

        // Step 2: Find course by course_id
        const selectedCourse = this.courseSubjectMap.find(
          (course) => Number(course.id) === Number(courseId)
        );

        if (selectedCourse) {
          // Step 3: Find subject where subject.id == subCourseId
          const selectedSubject = selectedCourse.subjects.find(
            (subject) => Number(subject.id) === Number(subCourseId)
          );

          if (selectedSubject) {
            this.selectedCategory = selectedSubject.title;
            this.currentSubjectId = selectedSubject.id;
            console.log("Extracted subject_id:", this.currentSubjectId);
          } else {
            console.warn("Subject not found for subCourseId:", subCourseId);
            this.selectedCategory = null;
            this.currentSubjectId = null;
          }
        } else {
          console.warn("Course not found for courseId:", courseId);
          this.selectedCategory = null;
          this.currentSubjectId = null;
        }

        this.currentSubCourseId = subCourseId;

        // Step 4: Fetch PDF data list
        const dataUrl = `https://cms.trehousingpublication.com/api/v2/?course_id=${courseId}&sub_courses=${subCourseId}`;
        const dataRes = await axios.get(dataUrl);
        this.pdfData = dataRes.data || {};
      } catch (error) {
        console.error("Error fetching data:", error);
        this.pdfData = {};
        this.selectedCategory = null;
        this.currentSubCourseId = null;
        this.currentSubjectId = null;
      } finally {
        this.isLoading = false;
      }
    },

    // Step 5: Use the subject_id (correctly extracted) in the PDF URL
    getPdfUrl(filename) {
      const courseId = this.$route.query.course_id;
      const subCourseId = this.currentSubCourseId;
      const subjectId = this.currentSubjectId;

      console.log("📄 Generating PDF URL with subject_id:", subjectId);

      if (!subjectId) {
        console.warn("Missing subject_id, cannot generate PDF URL");
        return "#";
      }

      return `https://cms.trehousingpublication.com/api/v2/?course_id=${courseId}&sub_courses=${subCourseId}&subject_id=${subjectId}&file=${encodeURIComponent(
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
  display: flex;
  justify-content: center;
  text-decoration: none;
  text-align: center;
  color: rgb(208, 5, 35);
}

.heading-pdf h4 {
  text-align: center;
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
  width: auto;
  border-collapse: collapse;
  margin: 0 auto;
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
