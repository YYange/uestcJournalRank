<template>
  <div class="journal-container">
    <div class="sidebar">
      <h3>期刊分类</h3>
      <ul>
        <li v-for="(group, category) in groupedJournals" :key="category">
          <!-- <a href="javascript:void(0)" @click="scrollToCategory(category)">{{ category }}</a> -->
          <ul>
            <li v-for="(subGroup, subCategory) in group" :key="subCategory">
              <a href="javascript:void(0)" @click="scrollToSubCategory(category, subCategory)">{{ subCategory }}</a>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    
    <div class="journal-table">
      <h1>电子科技大学经济与管理学院期刊评级</h1>
      
      <div v-for="(group, category) in groupedJournals" :key="category" class="category-group" :id="'category-' + category">
        <h2 class="category-title">{{ category }}</h2>
        
        <div v-for="(subGroup, subCategory) in group" :key="subCategory" class="subcategory-group" :id="'subcategory-' + category + '-' + subCategory">
          <h3 class="subcategory-title">{{ subCategory }}</h3>
          
          <div v-for="(ratingGroup, rating) in subGroup" :key="rating" class="rating-group">
            <h4 class="rating-title" style="text-align: left;">{{ rating }}</h4>
            
            <table class="journal-table">
              <thead>
                <tr>
                  <th>期刊名称</th>
                  <th>ISSN</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(journal, index) in ratingGroup" :key="index">
                  <td>{{ journal.name }}</td>
                  <td>{{ journal.issn }}</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: 'JournalTable',
  data() {
    return {
      journals: []
    }
  },
  computed: {
    groupedJournals() {
      const groups = {};
      this.journals.forEach(journal => {
        const [category, subCategory] = journal.category.split(' - ');
        if (!groups[category]) groups[category] = {};
        if (!groups[category][subCategory]) groups[category][subCategory] = {};
        if (!groups[category][subCategory][journal.rating]) groups[category][subCategory][journal.rating] = [];
        groups[category][subCategory][journal.rating].push(journal);
      });
      return groups;
    }
  },
  methods: {
    scrollToCategory(category) {
      const element = document.getElementById('category-' + category);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
      }
    },
    scrollToSubCategory(category, subCategory) {
      const element = document.getElementById('subcategory-' + category + '-' + subCategory);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth' });
      }
    }
  },
  created() {
    fetch('/paperMenu.json')
  .then(response => {
    console.log('HTTP响应状态:', response.status);
    if (!response.ok) {
      throw new Error(`HTTP错误! 状态: ${response.status}`);
    }
    return response.json();
  })
  .then(data => {
    console.log('原始数据:', data);
    const journals = [];
    
    // 处理数据
    console.log('开始处理数据...');
    for (const category in data) {
      console.log('当前类别:', category);
      for (const subCategory in data[category]) {
        console.log('当前子类别:', subCategory);
        for (const rating in data[category][subCategory]) {
          if (Array.isArray(data[category][subCategory][rating])) {
            console.log('找到期刊数组，长度:', data[category][subCategory][rating].length);
            data[category][subCategory][rating].forEach(journal => {
              journals.push({
                name: journal['名称'],
                category: `${category} - ${subCategory}`,
                rating: rating,
                issn: journal['ISSN']
              });
            });
          } else {
            console.warn('非数组数据:', data[category][subCategory][rating]);
          }
        }
      }
    }
    
    console.log('处理后数据:', journals);
    this.journals = journals;
  })
      .catch(error => {
        console.error('加载数据出错:', error);
        console.error('完整错误:', error.stack);
      });
  }
}
</script>

<style scoped>
.journal-container {
  display: flex;
  flex-direction: row;
  flex:1
}


.sidebar {
  background-color: #ffffff;
  width: 10%;
  box-sizing: border-box;
  padding: 10px;
  position: sticky;
  top: 0;
  left: 0;
  width: 20%;
  height: 100vh;
  overflow-y: auto;
  align-self: flex-start;
}

.sidebar h3 {
  color: #007bff;
  margin-bottom: 10px;
}

.sidebar ul {
  list-style: none;
  padding: 0;
}

.sidebar li:first-child {
  border-bottom: 1px solid #ccc;
}

.sidebar li:last-child {
  border-bottom: none;
}
.sidebar li {
  margin-bottom: 5px;
  border-bottom: 1px solid #ccc;
}

.sidebar a {
  color: #495057;
  text-decoration: none;
  display: block;
  padding: 3px;
  border-radius: 4px;
}

.sidebar a:hover {
  background: #e9ecef;
}

.journal-table {
  flex: 1;
  font-family: Arial, sans-serif;
  margin: 20px auto;
  max-width: 1000px;
}
.category-group {
  margin-bottom: 30px;
  background: #f8f9fa;
  padding: 15px;
  border-radius: 8px;
}
h1 {
  color: #007bff;
  border-bottom: 2px solid #007bff;
  padding-bottom: 8px;
  margin: 0 auto 15px;
  text-align: center;
  width: fit-content;
}
.subcategory-group {
  margin: 0 auto 20px;
  text-align: center;
  width: 80%;
  margin: 0 auto;
  padding: 10px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.subcategory-title {
  color: #495057;
  font-size: 1.1em;
  margin: 0 auto 10px;
  text-align: center;
  width: fit-content;
}
.rating-group {
  margin-left: 40px;
  margin-bottom: 15px;
}
.rating-title {
  color: #6c757d;
  font-size: 1em;
  font-weight: normal;
  margin-bottom: 5px;
}
table.journal-table {
  width: 100%;
  border-collapse: collapse;
  margin: 10px auto;
}
th, td {
  padding: 8px 12px;
  text-align: center;
  border-bottom: 1px solid #dee2e6;
}
th {
  background-color: #e9ecef;
  color: #212529;
  font-weight: 500;
}
tr:hover {
  background-color: #f1f8ff;
}
tr:hover {
  background-color: #f1f1f1;
}
.content {
  margin-left: 15%;
  flex-grow: 1;
  overflow-x: auto;
  overflow-y: auto;
}
</style>