<template>
  <div class="wallet-container">
    <el-dropdown @command="handleCommand">
       <span class="avatar-dropdown">
         <div class="user-name">
          {{ account ? account : '连接钱包' }}
          <i class="el-icon-arrow-down el-icon--right"></i>
         </div>
      </span>

      <el-dropdown-menu slot="dropdown">
        <template v-if="account">
          <el-dropdown-item command="copyAddress">📋 复制地址</el-dropdown-item>
          <el-dropdown-item command="switchWallet">🔄 切换钱包</el-dropdown-item>
          <el-dropdown-item command="logout">🚪 退出钱包</el-dropdown-item>
        </template>
        <template v-else>
          <el-dropdown-item command="metamask">🦊 MetaMask</el-dropdown-item>
          <el-dropdown-item command="tokenpocket">🛡️ TokenPocket</el-dropdown-item>
          <el-dropdown-item command="mathWallet">📱 MathWallet</el-dropdown-item>
          <el-dropdown-item command="bitget">🔗 Bitget</el-dropdown-item>
          <el-dropdown-item command="solflare">🔥 Solflare</el-dropdown-item>
        </template>
      </el-dropdown-menu>
    </el-dropdown>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { recordRoute } from '@/config'

export default {
  name: 'WalletSelector',
  computed: {
    ...mapGetters({
      avatar: 'user/avatar',
      username: 'user/username',
    }),
  },
  data() {
    return {
      account: null,
      networkName: '',
      selectedWallet: null,
    }
  },
  methods: {
    handleCommand(command) {
      switch (command) {
        case 'logout':
          this.logout()
          break
        case 'switchWallet':
          this.switchWallet()
          break
        case 'copyAddress':
          this.copyAddress()
          break
        default:
          this.connectWallet(command)
          break
      }
    },

    copyAddress() {
      if (this.account) {
        navigator.clipboard.writeText(this.account).then(() => {
          this.$message.success('地址已复制到剪贴板！')
        }).catch(err => {
          console.error('复制失败:', err)
        })
      }
    },

    switchWallet() {
      this.account = null
      this.selectedWallet = null
    },

    logout() {
      // 清空前端状态
      this.account = null
      this.selectedWallet = null

      // 让浏览器插件失效：清除 provider 对象（这在实际应用中能“模拟”退出钱包）
      if (window.ethereum) {
        window.ethereum._state.accounts = [] // 清除已连接的账户
        window.ethereum.selectedAddress = null // 清空选中的地址
      }

      // 提示用户
      this.$message.success('已退出钱包')

      // 如果你想刷新页面，也可以选择以下方法之一：
      // location.reload() // 强制刷新页面
    },

    async connectWallet(wallet) {
      this.selectedWallet = wallet

      let provider = null
      let extensionId = ''
      switch (wallet) {
        case 'metamask':
          provider = window.ethereum
          extensionId = 'nkbihfbeogaeaoehlefnkodbefgpgknn'  // MetaMask 扩展 ID
          break
        case 'tokenpocket':
          provider = window.ethereum?.isTokenPocket ? window.ethereum : null
          extensionId = 'plbmbjmdbecmjpfnfcdmhjkgjmghplpp'  // TokenPocket 扩展 ID
          break
        case 'mathWallet':
          provider = window.ethereum?.isMathWallet ? window.ethereum : null
          extensionId = 'afbcbjpbpfadlkmhmclhkeeodmamcflc'  // MathWallet 扩展 ID
          break
        case 'bitget':
          provider = window.bitkeep?.ethereum
          extensionId = 'jinjaccalgkegednnccohejagnlnfdag'  // Bitget 扩展 ID
          break
        case 'solflare':
          provider = window.solflare?.ethereum
          extensionId = 'nknhiehlklippafakaeklbeglecifhad'  // Solflare 扩展 ID
          break
      }

      if (!provider) {
        alert(`${wallet} 扩展程序未安装，请安装并启用该钱包！`)
        this.openExtension(extensionId)
        return
      }

      this.connectToWallet(provider)
    },

    async connectToWallet(provider) {
      try {
        let accounts = []

        if (provider.request) {
          // 尝试连接
          accounts = await provider.request({ method: 'eth_requestAccounts' })
        } else if (provider.enable) {
          // 旧版钱包支持 enable
          accounts = await provider.enable()
        }

        if (accounts.length === 0) {
          alert('请创建或导入钱包地址！')
          return
        }

        this.account = accounts[0]

        // 获取当前网络
        const chainId = await provider.request({ method: 'eth_chainId' })
        this.networkName = this.getNetworkName(chainId)
      } catch (error) {
        console.error('连接钱包失败:', error)
        if (error.code === 4001) {
          alert('用户拒绝了连接请求！')
        } else {
          alert('连接钱包时发生错误，请检查钱包扩展是否启用！')
        }
      }
    },

    getNetworkName(chainId) {
      const networks = {
        '0x1': 'Ethereum 主网',
        '0x3': 'Ropsten 测试网',
        '0x4': 'Rinkeby 测试网',
        '0x5': 'Goerli 测试网',
        '0x38': 'BNB Smart Chain',
        '0x89': 'Polygon',
      }
      return networks[chainId] || `未知网络 (${chainId})`
    },

    openExtension(extensionId) {
      const url = `chrome://extensions/?id=${extensionId}`
      window.open(url, '_blank')
    },
  },
}
</script>

<style lang="scss" scoped>
.avatar-dropdown {
  display: flex;
  align-content: center;
  align-items: center;
  justify-content: center;
  justify-items: center;
  height: 50px;
  padding: 0;

  .user-avatar {
    width: 40px;
    height: 40px;
    cursor: pointer;
    border-radius: 50%;
  }

  .user-name {
    position: relative;
    margin-left: 5px;
    cursor: pointer;
  }
}
</style>
