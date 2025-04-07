<template>
  <div class="form-container">
    <el-row :gutter="20">
      <el-col :lg="8" :md="12" :sm="24" :xl="8" :xs="24">
        <el-form ref="ruleForm" class="demo-ruleForm" label-width="100px" :model="ruleForm" :rules="rules">
          <el-form-item label="Token名称" prop="name">
            <el-input v-model="ruleForm.name" placeholder="请输入Token名称" />
          </el-form-item>

          <el-form-item label="Token符号" prop="symbol">
            <el-input v-model="ruleForm.symbol" placeholder="请输入Token符号" />
          </el-form-item>

          <el-form-item label="Token精度" prop="precision">
            <el-input v-model="ruleForm.precision" placeholder="请输入Token精度" />
          </el-form-item>

          <el-form-item label="Token总数" prop="totalSupply">
            <el-input v-model="ruleForm.totalSupply" placeholder="请输入Token总数" />
          </el-form-item>

          <el-form-item label="Token描述" prop="description">
            <el-input v-model="ruleForm.description" placeholder="请输入Token描述" type="textarea" />
          </el-form-item>

          <el-form-item label="Token Logo" prop="logo">
            <vab-upload ref="vabUpload" :limit="50" name="file" :size="2" url="/upload" />
            <el-button type="primary" @click="handleShow({ key: 'value' })">模拟上传</el-button>
          </el-form-item>

          <el-form-item label="添加社交链接" prop="socialLinks">
            <el-input v-model="ruleForm.socialLinks" placeholder="输入Token社交链接，如 GitHub" />
          </el-form-item>

          <el-form-item label="放弃元数据权限" prop="disableMetadata">
            <el-switch v-model="ruleForm.disableMetadata" />
          </el-form-item>
          <el-form-item label="放弃销毁权限" prop="disableDestruction">
            <el-switch v-model="ruleForm.disableDestruction" />
          </el-form-item>

          <el-form-item>
            <el-button type="primary" @click="submitForm('ruleForm')">创建代币</el-button>
            <el-button @click="resetForm('ruleForm')">重置</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import VabUpload from '../../../components/VabUpload/index.vue'

  export default {
    name: 'CreateCoins', // Ensure PascalCase for component names
    components: {
      VabUpload,
    },
    data() {
      return {
        ruleForm: {
          name: '',
          symbol: '',
          precision: '',
          totalSupply: '',
          description: '',
          logo: null,
          socialLinks: '',
          disableMetadata: false,
          disableDestruction: false,
        },
        rules: {
          name: [{ required: true, message: '请输入Token名称', trigger: 'blur' }],
          symbol: [{ required: true, message: '请输入Token符号', trigger: 'blur' }],
          precision: [{ required: true, message: '请输入Token精度', trigger: 'blur' }],
          totalSupply: [{ required: true, message: '请输入Token总数', trigger: 'blur' }],
          description: [{ required: true, message: '请输入Token描述', trigger: 'blur' }],
          socialLinks: [{ required: false, message: '请输入Token社交链接', trigger: 'blur' }],
        },
      }
    },
    methods: {
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('创建代币成功！')
          } else {
            return false
          }
        })
      },
      resetForm(formName) {
        this.$refs[formName].resetFields()
      },
      handleShow(data) {
        this.$refs['vabUpload'].handleShow(data)
      },
    },
  }
</script>

<style lang="scss" scoped>
  .form-container {
    padding: 20px;

    .upload-demo {
      margin-top: 10px;
    }

    .el-form-item {
      margin-bottom: 20px;
    }
  }
</style>
