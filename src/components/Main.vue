<template>
  <div>
    <div class="wrap mt-3">
      <div class="botSays top8" :class="{ bot: botSays, editSays: botSaysEdit, pathSays: botSaysPath }">
        <div class="btn btn-lg btn-secondary rounded-pill ml-auto" @click.capture="openSays('bot')">
          <div class="d-flex justify-content-around">
            <p class="ml-auto mb-0" v-if="!botSaysEdit">BOT SAYS</p>
            <p class="ml-auto mb-0" v-if="botSaysEdit">EDIT MESSAGE</p>
            <p class="ml-auto mt-1 cursor-pointer mb-0" v-if="botSays || botSaysEdit || botSaysPath" @click="offSays('bot')"><i class="material-icons">close</i></p>
          </div>
          <section class="mt-3" v-if="botSays || botSaysEdit || botSaysPath">
            <ul class="nav">
              <li class="ml-3"><a href="#" class="px-2 pt-2 rounded-top text-white" :class="{ active: botSaysType === 'text' }" @click="changeSaysType(false, 'text')"><i class="material-icons">text_format</i></a></li>
              <li class="ml-3"><a href="#" class="px-2 pt-2 rounded-top text-white" :class="{ active: botSaysType === 'image' }" @click="changeSaysType(false, 'image')"><i class="material-icons">add_a_photo</i></a></li>
              <li class="ml-3"><a href="#" class="px-2 pt-2 rounded-top text-white" :class="{ active: botSaysType === 'button' }" @click="changeSaysType(false, 'button')" v-if="!botSaysPath"><i class="material-icons">view_list</i></a></li>
            </ul>
            <form>
              <div class="bg-white ml-3 d-none" :class="{ 'd-block': botSaysType === 'text' }">
                <textarea class="bg-text border-0 my-3 rounded" style="width: 70%; height: 100px;" name="text" rows="10" placeholder="Type a message..." v-model="tempAddMessage.bot.message"></textarea>
              </div>
              <div class="bg-white ml-3 pb-3 d-none" :class="{ 'd-block': botSaysType === 'image' }">
                <textarea class="bg-text border-0 my-3 rounded-top" style="width: 70%; height: 50px;" name="text" rows="10" placeholder="Text (optional)" v-model="tempAddMessage.bot.message"></textarea>
                <label class="addImage rounded-bottom cursor-pointer" for="file-uploaderBot" v-if="tempAddMessage.bot.image === ''">
                  <i class="material-icons mt-5">add<p>ADD IMAGE</p></i>
                </label>
                <label class="addImage rounded-bottom cursor-pointer" for="file-uploaderBot" v-if="tempAddMessage.bot.image !== ''" :style="{backgroundImage: 'url(' + tempAddMessage.bot.image + ')'}">
                  <div class="imageReoplace">
                    <i class="material-icons mt-5 ">photo_camera</i>
                    <p>Replace</p>
                  </div>
                </label>
                <input type="file" id="file-uploaderBot" @change="fileUploader('bot')" ref="bot" style="display:none">
              </div>
              <div class="bg-white ml-3 pb-3 d-none" :class="{ 'd-block': botSaysType === 'button' }">
                <textarea class="bg-text border-0 my-3 rounded-top" style="width: 70%; height: 70px;" name="text" rows="10" placeholder="Text" v-model="tempAddMessage.bot.message"></textarea>
                <div class="addButton rounded-bottom cursor-pointer">
                  <input class="border-0 my-2 rounded" type="text" placeholder="Button 1" v-model="tempAddMessage.bot.button[0]">
                  <input class="border-0 my-2 rounded" type="text" placeholder="Button 2" v-model="tempAddMessage.bot.button[1]">
                  <input class="border-0 my-2 rounded" type="text" placeholder="Button 3" v-model="tempAddMessage.bot.button[2]">
                </div>
              </div>
            </form>
            <div class="d-flex text-secondary1 mt-3">
              <button class="ml-auto mr-4 btn btn-link bg-white rounded-pill d-flex font-weight-bold d-flex justify-content-center" style="width:110px;" @click="addMessage('bot', botSaysEdit, botSaysPath)">{{ ADDString.bot }}<i class="ml-2 material-icons">message</i></button>
            </div>
          </section>
        </div>
      </div>
      <section class="phome">
        <div class="phomeMessage">
          <div class="d-flex">
            <p class="pl-4 pt-1 mr-auto">15:03</p>
            <div class="systemrow d-flex pr-2 pb-2">
              <div class="phonesignal align-self-center"></div>
              <div class="wifisignal align-self-center mx-1"></div>
              <div class="batteryIcon align-self-center"></div>
            </div>
          </div>
          <div class="messengerTopRow">
            <div class="backarrow"></div>
            <div class="profile-picture"></div>
            <div class="botname">your bot</div>
            <div class="bot_subtitle">Typically replies instantly</div>
            <div class="block">
              <span class="thick">
                <i class="material-icons text-white" role="img" aria-label="done">done</i>
              </span><span class="dropdown">
                <i class="material-icons text-white" role="img" aria-label="arrow_drop_down">arrow_drop_down</i>
              </span>
            </div>
          </div>
          <div class="message" @scroll="scroll">
            <div class="d-flex align-items-center flex-column">
              <div class="profile-picture profile-picture-bg m-0 position-static"></div>
              <p class="m-0 botname">your bot</p>
              <p class="m-0 fans">0 people like this</p>
              <p class="m-0 page-category">App page</p>
            </div>
            <div class="messageBlock cursor-pointer" :class="{ 'active': saysEditID === item.id }"  @mouseenter="mouseenter(item.id, item.user)" @mouseleave='mouseleave' @click="editMessage(item.id, item.user, true)" v-for="item in filterMessage" :key="item.id" :id='item.id'>
              <div class="d-flex mx-2 mb-1" v-if="item.type === 'text' " :class="{ 'messageUser': item.user }">
                <div class="profile-picture profile-picture-sm m-0 position-static align-self-center" v-if="!item.user"></div>
                <div class="align-self-center messageText ml-3 bg-text" v-if="!(((botSaysPath && !item.user) || (userSaysPath && item.user))  && (item.id === tempAddMessage.bot.id || item.id === tempAddMessage.user.id))">{{ item.message }}</div>
                <div class="align-self-center messageText ml-3 text-white" style="background-color: #47bd21 !important; border-color: #47bd21 !important;" v-if="((botSaysPath && !item.user) || (userSaysPath && item.user))  && (item.id === tempAddMessage.bot.id || item.id === tempAddMessage.user.id)">Your message will be shown here</div>
              </div>
              <div class="d-flex mx-2 mb-1" v-if="item.type === 'image' " :class="{ 'messageUser': item.user }">
                <div class="profile-picture profile-picture-sm m-0 position-static align-self-end" v-if="!item.user"></div>
                <div class="align-self-center messageText ml-3 text-white" style="background-color: #47bd21 !important; border-color: #47bd21 !important;" v-if="((botSaysPath && !item.user) || (userSaysPath && item.user))  && (item.id === tempAddMessage.bot.id || item.id === tempAddMessage.user.id)">Your message will be shown here</div>
                <div class="messageImage d-flex flex-column ml-3" v-if="!(((botSaysPath && !item.user) || (userSaysPath && item.user))  && (item.id === tempAddMessage.bot.id || item.id === tempAddMessage.user.id))">
                  <div class="align-self-center messageImageText bg-text" v-if="item.message !== ''">{{ item.message }}</div>
                  <img class="img-fluid" :class="{ 'messageImageBorderRadius': item.message !== ''}" :src="item.image" alt="">
                </div>
              </div>
              <div class="d-flex mx-2 mb-1" v-if="item.type === 'button' " :class="{ 'messageUser': item.user }">
                <div class="profile-picture profile-picture-sm m-0 position-static align-self-center" v-if="!item.user"></div>
                <div class="messageButton d-flex flex-column ml-3 bg-text">
                  <div class="p-2">{{ item.message }}</div>
                  <div class="messageButtonBar">
                    <div class="messageButton-btn py-2 cursor-pointer" @click="changeButtonPath(item.id, index)" :class="{ 'active': item.path === index}" v-for="(itemButton, index) in item.button" :key="index">{{itemButton}}</div>
                  </div>
                </div>
              </div>
            </div>
            <div class="d-flex align-items-center flex-column my-3" v-if="message.length !== 0">
              <div class="row justify-content-center flex-column">
                <p>What should happen now?</p>
                <a class="col-12 d-flex align-items-center flex-column bg-text p-3 cursor-pointer" @click.prevent="openSays('bot')">
                  <i class="material-icons">message</i><span>ADD NEW MESSAGE</span>
                </a>
              </div>
            </div>
          </div>
          <div class="phomeFooter">
            <div class="d-flex d-flex justify-content-around mx-2">
              <div class="phomeFooter-button align-self-center" style="background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/footer_icons/fb_extension.svg);"></div>
              <div class="phomeFooter-button align-self-center" style="background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/footer_icons/fb_camera.svg);"></div>
              <div class="phomeFooter-button align-self-center" style="background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/footer_icons/fb_image.svg);"></div>
              <div class="phomeFooter-button micro_button align-self-center" style="background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/footer_icons/fb_microphone.svg);"></div>
              <div class="d-flex messageInput">
                <div class="align-self-center ml-2">Aa</div>
                <div class="input_emoticon ml-auto align-self-center mr-2"></div>
              </div>
              <div class="phomeFooter-button align-self-center" style="background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/footer_icons/fb_menu.svg);"></div>
            </div>
            <div class="d-flex justify-content-center mt-2">
              <!-- <div class="homebar mx-auto my-3"></div> -->
              <img class="homebar" src="https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/homebar.png" alt="">
            </div>
          </div>
        </div>
        <div class="featuresBar d-none" :class="{ 'd-flex': featuresBarY !== 0}">
          <div class="featuresBar-left d-flex flex-column align-items-center justify-content-around" :style="{ top: filterFeaturesBarY + 'px'}">
            <div class="featuresBar-i bg-white" @click="arrow(false)" :class="{ disabled: (tempFeaturesBarMessage.showPath === 0 && tempFeaturesBarMessage.type !== 'button') || (tempFeaturesBarMessage.path === 0 && tempFeaturesBarMessage.type === 'button') }">
              <i class="material-icons">arrow_back</i>
            </div>
            <div class="featuresBar-i bg-white" @click="editMessage(saysEditID, saysEditUser, false)">
              <i class="material-icons">edit</i>
            </div>
            <div class="featuresBar-i bg-white" @click="deleteMessage">
              <i class="material-icons">delete</i>
            </div>
          </div>
          <div class="featuresBar-right d-flex flex-column align-items-center justify-content-around" :style="{ top: filterFeaturesBarY + 'px'}">
            <div class="featuresBar-i bg-white" @click="arrow(true)" :class="{ disabled: ( tempFeaturesBarMessage.showPath + 1 === tempFeaturesBarMessage.showID.length && tempFeaturesBarMessage.type !== 'button' ) || (tempFeaturesBarMessage.path === (tempFeaturesBarMessage.button.length - 1) && tempFeaturesBarMessage.type === 'button')}">
              <i class="material-icons">arrow_forward</i>
            </div>
            <div class="featuresBar-i bg-white" @click="addPath" v-if="tempFeaturesBarMessage.type !== 'button'">
              <img src="https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/path_icon_blue.svg">
            </div>
            <div class="featuresBar-i bg-white disabled">
              <i class="material-icons">comment</i>
            </div>
          </div>
        </div>
      </section>
      <div class="userSays top8">
        <div class="" :class="{ user: userSays, editSays:userSaysEdit, pathSays: userSaysPath }">
          <div class="btn btn-lg btn-primary rounded-pill" @click.capture="openSays('user')">
            <div class="d-flex justify-content-around">
              <p class="ml-auto mb-0" v-if="!userSaysEdit">USER SAYS</p>
              <p class="ml-auto mb-0" v-if="userSaysEdit">EDIT MESSAGE</p>
              <p class="ml-auto mt-1 cursor-pointer mb-0" v-if="userSays || userSaysEdit || userSaysPath" @click="offSays('user')"><i class="material-icons">close</i></p>
            </div>
            <section class="mt-3" v-if="userSays || userSaysEdit || userSaysPath">
              <ul class="nav">
                <li class="ml-3"><a href="#" class="px-2 pt-2 rounded-top text-white" :class="{ active: userSaysType === 'text' }" @click="changeSaysType(true, 'text')"><i class="material-icons">text_format</i></a></li>
                <li class="ml-3"><a href="#" class="px-2 pt-2 rounded-top text-white" :class="{ active: userSaysType === 'image' }" @click="changeSaysType(true, 'image')"><i class="material-icons">add_a_photo</i></a></li>
              </ul>
              <form>
                <div class="bg-white ml-3 d-none" :class="{ 'd-block': userSaysType === 'text' }">
                  <textarea class="bg-text border-0 my-3 rounded" style="width: 70%; height: 100px;" name="text" id="" rows="10" placeholder="Type a message..." v-model="tempAddMessage.user.message"></textarea>
                </div>
                <div class="bg-white ml-3 pb-3 d-none" :class="{ 'd-block': userSaysType === 'image' }">
                  <textarea class="bg-text border-0 my-3 rounded-top" style="width: 70%; height: 50px;" name="text" id="" rows="10" placeholder="Text (optional)" v-model="tempAddMessage.user.message"></textarea>
                  <label class="addImage rounded-bottom cursor-pointer" for="file-uploaderUser" v-if="tempAddMessage.user.image === ''">
                    <i class="material-icons mt-5">add<p>ADD IMAGE</p></i>
                  </label>
                  <label class="addImage rounded-bottom cursor-pointer" for="file-uploaderUser" v-if="tempAddMessage.user.image !== ''" :style="{backgroundImage: 'url(' + tempAddMessage.user.image + ')'}">
                    <div class="imageReoplace">
                      <i class="material-icons mt-5 ">photo_camera</i>
                      <p>Replace</p>
                    </div>
                  </label>
                  <input type="file" id="file-uploaderUser" @change="fileUploader('user')" ref="user" style="display:none">
                </div>
              </form>
              <div class="d-flex text-secondary1 mt-3">
                <button class="ml-auto mr-4 btn btn-link bg-white rounded-pill d-flex font-weight-bold d-flex justify-content-center" style="width:110px;" @click="addMessage('user', userSaysEdit, userSaysPath)">{{ ADDString.user }}<i class="ml-2 material-icons">message</i></button>
              </div>
            </section>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import $ from 'jquery'

export default {
  props: {
    msg: String
  },
  data () {
    return {
      botSays: false,
      userSays: false,
      botSaysEdit: false,
      userSaysEdit: false,
      botSaysPath: false,
      userSaysPath: false,
      saysEditID: null,
      saysEditUser: false,
      botSaysType: 'text',
      userSaysType: 'text',
      message: [
        {
          id: 1,
          user: true,
          type: 'text',
          message: '我想訂房1',
          path: 0,
          showID: [1, 6],
          button: [],
          next: [2, 4]
        },
        {
          id: 2,
          user: false,
          type: 'text',
          message: '我想訂房2',
          path: 0,
          showID: [2],
          button: [],
          next: [3]
        },
        {
          id: 3,
          user: true,
          type: 'text',
          message: '我想訂房3',
          path: 0,
          showID: [3],
          next: [1566036643889]
        },
        {
          id: 6,
          user: true,
          type: 'text',
          message: '我想訂房12',
          path: 1,
          showID: [1, 6],
          button: [],
          next: [2, 4]
        },
        {
          id: 4,
          user: true,
          type: 'text',
          showID: [4],
          message: '我想訂房4',
          path: 0,
          next: []
        },
        {
          id: 1566036643889,
          user: false,
          type: 'image',
          message: '123',
          image: 'https://i.imgur.com/4tQA11z.jpg',
          path: 0,
          button: [],
          showID: [1566036643889],
          next: [5]
        },
        {
          id: 5,
          user: false,
          type: 'button',
          message: '123',
          path: 0,
          next: [],
          showID: [5],
          button: ['zxc', 'qwe', 'asd']
        }
      ],
      messageStartId: 1,
      messageEndId: null,
      tempAddMessage: {
        bot: {
          id: 1,
          user: false,
          type: 'text',
          message: '',
          image: '',
          path: 0,
          showPath: 0,
          showID: [],
          next: [],
          button: []
        },
        user: {
          id: 2,
          user: true,
          type: 'text',
          message: '',
          image: '',
          path: 0,
          showPath: 0,
          showID: [],
          next: [],
          button: []
        }
      },
      tempFeaturesBarMessage: {
        id: 1,
        user: false,
        type: 'text',
        message: 'bot',
        image: '',
        path: 0,
        showPath: 0,
        showID: [],
        next: [],
        button: []
      },
      ADDString: {
        bot: 'ADD',
        user: 'ADD'
      },
      mouseenterTime: null,
      featuresBarY: 0,
      messageY: 0
    }
  },
  methods: {
    openSays (says) {
      const vm = this
      if (says === 'bot' && !vm.botSays) {
        vm.botSays = !vm.botSays
      }
      if (says === 'user' && !vm.userSays) {
        vm.userSays = !vm.userSays
      }
    },
    offSays (says) {
      const vm = this
      vm.saysEditID = null
      if (says === 'bot') {
        if (vm.botSaysPath) {
          vm.botSaysPath = false
          vm.botSaysEdit = false
          vm.tempAddMessageInit('bot')
          return
        }
        if (vm.botSaysEdit) {
          vm.botSaysEdit = false
          vm.tempAddMessageInit('bot')
          vm.ADDString.bot = 'ADD'
          return
        }
        vm.botSays = false
      } else {
        if (vm.userSaysPath) {
          vm.userSaysEdit = false
          vm.userSaysPath = false
          vm.tempAddMessageInit('bot')
          return
        }
        if (vm.userSaysEdit) {
          vm.userSaysEdit = false
          vm.tempAddMessageInit('user')
          vm.ADDString.user = 'ADD'
          return
        }
        vm.userSays = false
      }
    },
    changeSaysType (user, SaysType) {
      const vm = this
      if (!user) {
        if (vm.botSaysEdit) { return }
        vm.botSaysType = SaysType
        vm.tempAddMessage.bot.type = SaysType
        vm.tempAddMessage.bot.message = ''
      } else {
        if (vm.userSaysEdit) { return }
        vm.userSaysType = SaysType
        vm.tempAddMessage.user.type = SaysType
        vm.tempAddMessage.user.message = ''
      }
    },
    addMessage (user, SaysEdit, SaysPath) {
      const vm = this
      let timesTamp = new Date().getTime()
      let endIndex = vm.message.findIndex(function (item) {
        return item.id === vm.messageEndId
      })
      let showIndex = vm.message.findIndex(function (item) {
        return item.id === vm.saysEditID
      })
      if (SaysPath) {
        vm.message[showIndex].showID.push(timesTamp)
        vm.message[showIndex].showPath = vm.message[showIndex].showID.length - 1

        vm.tempAddMessage[user].id = timesTamp
        vm.tempAddMessage[user].showPath = vm.message[showIndex].showPath
        vm.tempAddMessage[user].showID = vm.message[showIndex].showID

        vm.message.push(Object.assign({}, vm.tempAddMessage[user]))

        vm.message.forEach(function (item) {
          if (item.showID === vm.message[showIndex].showID) {
            item.showPath = vm.message[showIndex].showPath
          }
        })

        // vm.message.push(Object.assign({}, vm.tempAddMessage[user]))
        // 更新 FeaturesBar 狀態
        vm.tempFeaturesBarMessage = Object.assign({}, vm.tempAddMessage[user])
        vm.saysEditID = vm.tempAddMessage[user].id
        vm.botSaysPath = false
        vm.userSaysPath = false
        vm.tempAddMessageInit(user)
        return
      }
      if (SaysEdit) {
        let index = vm.message.findIndex(function (item) {
          return item.id === vm.saysEditID
        })
        // vm.message[index] = vm.tempAddMessage[user]
        // vm.message[index] = Object.assign({}, vm.tempAddMessage[user])
        vm.message[index].message = vm.tempAddMessage[user].message
        vm.message[index].image = vm.tempAddMessage[user].image
        vm.message[index].button = vm.tempAddMessage[user].button
        console.log(vm.message[index])
        vm.botSaysEdit = false
        vm.userSaysEdit = false
        vm.tempAddMessageInit(user)
        return
      }
      // console.log(vm.message.length === 0)
      if (vm.message.length === 0) {
        vm.tempAddMessage[user].id = timesTamp
        vm.tempAddMessage[user].showID.push(timesTamp)
        vm.message.push(Object.assign({}, vm.tempAddMessage[user]))
        // vm.message[0] = Object.assign({}, vm.tempAddMessage[user])
        vm.messageStartId = timesTamp
        vm.tempAddMessageInit(user)
        return
      }
      vm.tempAddMessage[user].id = timesTamp
      vm.tempAddMessage[user].showID.push(timesTamp)

      vm.message[endIndex].next[vm.message[endIndex].path] = timesTamp
      console.log(vm.message[endIndex])
      vm.message.push(Object.assign({}, vm.tempAddMessage[user]))
      console.log(vm.message[vm.message.length - 1])
      console.log(vm.tempAddMessage[user])
      // var $div = $('.message')
      // console.log($div)
      // let a =$div[0].scrollHeight
      // $div.scrollTop(a+1000)
      // initial
      vm.tempAddMessageInit(user)
    },
    fileUploader (user) {
      const vm = this
      const accessToken = 'a23a8f96cb5ca9d14645693cce5ee6dd8cfc313b'
      const album = 'nbWD21o'
      // const uploadedFile = e.target.files[0]
      const uploadedFile = this.$refs[user].files[0]
      // console.log(e.target.files[0])
      console.log(this.$refs[user].files[0])
      const formData = new FormData()
      formData.append('image', uploadedFile)
      formData.append('title', uploadedFile.name)
      formData.append('album', album)
      const url = `https://api.imgur.com/3/image`
      this.$http.post(url, formData, {
        headers: {
          Authorization: 'Bearer ' + accessToken,
          'Content-Type': 'multipart/form-data'
        }
      }).then((response) => {
        console.log(response.data)
        console.log(response.data.data.link)
        if (response.data.success) {
          vm.$set(vm.tempAddMessage[user], 'image', response.data.data.link)
        }
      })
    },
    changeButtonPath (id, index) {
      const vm = this
      // console.log(id)
      let changeButtonIndex = vm.message.findIndex(function (item) {
        return id === item.id
      })
      vm.message[changeButtonIndex].path = index
    },
    editMessage (id, user, inMessageBlock) {
      const vm = this
      if (window.event.target.parentNode.className === 'messageButtonBar') { return }
      // featuresBar 位置
      if (vm.botSaysPath || vm.userSaysPath) {
        vm.botSaysPath = false
        vm.userSaysPath = false
      }
      if (inMessageBlock) { vm.featuresBarY = window.event.target.offsetTop }
      vm.saysEditID = id
      let changeButtonIndex = vm.message.findIndex(function (item) {
        return vm.saysEditID === item.id
      })
      let messageItem = vm.message.find(function (item) {
        return id === item.id
      })
      vm.tempFeaturesBarMessage = Object.assign({}, messageItem)
      if (!user) {
        vm.botSaysEdit = true
        vm.userSaysEdit = false
        vm.botSays = true
        vm.ADDString.bot = 'SAVE'
        vm.ADDString.user = 'ADD'
        vm.tempAddMessage.bot = Object.assign({}, vm.message[changeButtonIndex])
        vm.tempAddMessage.bot.button = Object.assign({}, vm.message[changeButtonIndex].button)
        vm.tempAddMessageInit('user')
        vm.botSaysType = vm.message[changeButtonIndex].type
      } else {
        vm.botSaysEdit = false
        vm.userSaysEdit = true
        vm.userSays = true
        vm.ADDString.bot = 'ADD'
        vm.ADDString.user = 'SAVE'
        vm.tempAddMessage.user = Object.assign({}, vm.message[changeButtonIndex])
        vm.tempAddMessageInit('bot')
        vm.userSaysType = vm.message[changeButtonIndex].type
      }
    },
    tempAddMessageInit (user) {
      const vm = this
      vm.tempAddMessage[user].next = []
      vm.tempAddMessage[user].message = ''
      vm.tempAddMessage[user].image = ''
      vm.tempAddMessage[user].button = []
      vm.tempAddMessage[user].showID = []
      vm.tempAddMessage[user].showPath = 0
    },
    mouseenter (id, user) {
      const vm = this
      clearTimeout(vm.mouseenterTime)
      let barY = window.event.target.offsetTop
      let messageY = window.event.path[1].scrollTop
      // console.log(window.event.path[1].scrollTop)
      // console.log(window.event.target.offsetTop)
      // console.log(window.event.clientY)
      let messageItem = vm.message.find(function (item) {
        return id === item.id
      })
      // let userName = 'bot'
      // if (user) {userName = 'user'}
      if (vm.botSaysEdit || vm.userSaysEdit) { return }
      vm.mouseenterTime = setTimeout(function () {
        vm.tempFeaturesBarMessage = Object.assign({}, messageItem)
        vm.saysEditID = id
        vm.saysEditUser = user
        vm.featuresBarY = barY
        vm.messageY = messageY
      }, 1000)
    },
    mouseleave () {
      const vm = this
      clearTimeout(vm.mouseenterTime)
      if (vm.botSaysEdit || vm.userSaysEdit || vm.botSaysPath || vm.userSaysPath) { return }
      vm.mouseenterTime = setTimeout(function () {
        vm.saysEditID = null
        vm.featuresBarY = 0
      }, 10000)
    },
    scroll (e) {
      const vm = this
      vm.messageY = e.target.scrollTop
    },
    addPath () {
      const vm = this
      vm.tempAddMessageInit('bot')
      vm.tempAddMessageInit('user')
      clearTimeout(vm.mouseenterTime)
      if (vm.botSaysPath || vm.userSaysPath) { return }
      // !vm.saysEditUser ? vm.botSaysPath = true : vm.userSaysPath = true
      console.log(vm.saysEditUser)
      if (!vm.tempFeaturesBarMessage.user) {
        vm.botSaysPath = true
      } else {
        vm.userSaysPath = true
      }
      vm.botSaysEdit = false
      vm.userSaysEdit = false
      // if(!vm.saysEditUser) {
      //   vm.botSaysPath = true
      // } else
      // console.log(vm.tempAddMessage)
      // console.log(vm.saysEditUser)
      // console.log(vm.saysEditID)
    },
    arrow (arrow) {
      // arrow false is back, arrow true is forward
      const vm = this
      let a = -1
      if (arrow) { a = 1 }
      if (vm.tempFeaturesBarMessage.type === 'button') {
        let changeButtonIndex = vm.messageIndex(vm.tempFeaturesBarMessage.id)
        console.log(arrow)
        console.log(vm.message[changeButtonIndex].path)
        console.log(vm.message[changeButtonIndex].next)
        if (arrow && vm.message[changeButtonIndex].path === vm.message[changeButtonIndex].button.length - 1) { return }
        if (!arrow && vm.message[changeButtonIndex].path === 0) { return }
        vm.message[changeButtonIndex].path = vm.message[changeButtonIndex].path + a

        vm.tempFeaturesBarMessage = Object.assign({}, vm.message[changeButtonIndex])
        vm.saysEditID = vm.message[changeButtonIndex].id
        return
      }
      if (vm.tempFeaturesBarMessage.showPath === 0 && !arrow) { return }
      if (vm.tempFeaturesBarMessage.showPath === vm.tempFeaturesBarMessage.showID.length - 1 && arrow) { return }
      const showPath = vm.tempFeaturesBarMessage.showPath
      let previousID = vm.tempFeaturesBarMessage.showID[showPath + a]

      vm.message.forEach(function (item) {
        if (item.showID === vm.tempFeaturesBarMessage.showID) {
          item.showPath = item.showPath + a
        }
      })

      vm.tempFeaturesBarMessage = Object.assign({}, vm.message[vm.messageIndex(previousID)])
      vm.saysEditID = vm.message[vm.messageIndex(previousID)].id
    },
    messageIndex (id) {
      const vm = this
      return vm.message.findIndex(function (item) {
        return id === item.id
      })
    },
    deleteMessage () {
      const vm = this
      let filterMessageIndex = vm.filterMessage.findIndex(function (item) {
        return vm.tempFeaturesBarMessage.id === item.id
      })
      if (vm.tempFeaturesBarMessage.showID.length > 1) {
        let deleteID = vm.tempFeaturesBarMessage.id
        let deleteShowIDIndex = vm.tempFeaturesBarMessage.showPath
        let previousShowID = null
        if (deleteShowIDIndex !== 0) {
          previousShowID = vm.tempFeaturesBarMessage.showID[deleteShowIDIndex - 1]
        }
        let nextShowID = null
        if (deleteShowIDIndex !== vm.tempFeaturesBarMessage.showID.length - 1) {
          nextShowID = vm.tempFeaturesBarMessage.showID[deleteShowIDIndex + 1]
        }
        vm.message[vm.messageIndex(deleteID)].showID.splice(deleteShowIDIndex, 1)
        vm.message.splice(vm.messageIndex(deleteID), 1)
        let i = -1
        if (deleteShowIDIndex === 0) { i = 0 }
        vm.message.forEach(function (item) {
          if (item.showID === vm.tempFeaturesBarMessage.showID) {
            item.showPath = item.showPath + i
          }
        })
        let StartID = previousShowID
        if (deleteShowIDIndex === 0) { StartID = nextShowID }
        vm.tempFeaturesBarMessage = Object.assign({}, vm.message[vm.messageIndex(StartID)])
        vm.saysEditID = StartID
        vm.messageStartId = StartID
      } else {
        // let deleteIndex = filterMessageIndex
        let previousID = null
        if (filterMessageIndex > 0) {
          previousID = vm.filterMessage[filterMessageIndex - 1].id
        }
        let deleteID = vm.tempFeaturesBarMessage.id
        let nextID = null
        if (vm.tempFeaturesBarMessage.next.length !== 0) {
          nextID = vm.tempFeaturesBarMessage.next[vm.tempFeaturesBarMessage.path]
        }
        if (previousID === null) {
          vm.messageStartId = nextID
        } else {
          vm.message[vm.messageIndex(previousID)].next[vm.message[vm.messageIndex(previousID)].path] = nextID
        }

        vm.message.splice(vm.messageIndex(deleteID), 1)
        vm.botSaysEdit = false
        vm.featuresBarY = 0
        vm.userSaysEdit = false
        vm.tempAddMessageInit('bot')
        vm.tempAddMessageInit('user')
        // console.log(previousID, deleteID, nextID)
      }
    },
    init () {
      const vm = this
      vm.message.filter(function (item) {
        if (item.id !== item.showID[item.path]) {
          vm.messageStartId = item.showID[item.path]
        }
      })
    }
  },
  computed: {
    filterMessage () {
      const vm = this
      let tempId = vm.messageStartId
      let tempMessage = []
      // vm.init()
      // console.log(vm.message)
      vm.message.filter(function (item) {
        // console.log(tempId === item.id && tempId === item.showID[item.path])
        // console.log('d')
        // console.log(tempId === item.id)
        // && tempId === item.showID[item.path]
        if (tempId === item.id && item.id === item.showID[item.showPath]) {
          tempMessage.push(item)
          tempId = item.next[item.path]
          // console.log('tempId')
          // console.log(tempId)
        } else if (tempId === item.id) {
          tempId = item.showID[item.showPath]
          // console.log(tempId)
        }
      })
      // console.log(tempMessage)
      if (tempMessage.length !== 0) {
        vm.messageEndId = tempMessage[tempMessage.length - 1].id
      }
      return tempMessage
    },
    filterFeaturesBarY () {
      const vm = this
      return vm.featuresBarY - vm.messageY
    }
  },
  created () {
    this.message = []
    this.messageStartId = null
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.phome {
  background-image: url(https://d2d9a1sv0xqzzt.cloudfront.net/node/022/images/iphonex_preview_silver.png);
  background-size: 390px;
  width: 390px;
  height: 768px;
  background-repeat: no-repeat;
}
</style>
