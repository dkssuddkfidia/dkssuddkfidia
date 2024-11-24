response = (o, m, s, i, r) => {
  if (m.startsWith("!변환 ")) 
    r.reply('[\n"' + m.substr(4).split("\n").join('"+"\\n"+\n"').split("[[5000]]").join('​'.repeat(5000)) + '"\n]');
};
function onNotificationPosted(sbn, sm) {
  var packageName = sbn.getPackageName();
  if (!packageName.startsWith("com.kakao.tal")) 
    return;
  var actions = sbn.getNotification().actions;
  if (actions == null) 
    return;
  var userId = sbn.getUser().hashCode();
  for (var n = 0; n < actions.length; n++) {
    var action = actions[n];
    if (action.getRemoteInputs() == null) 
      continue;
    var bundle = sbn.getNotification().extras;
    var msg = bundle.get("android.text").toString();
    var sender = bundle.getString("android.title");
    var room = bundle.getString("android.subText");
    if (room == null) 
      room = bundle.getString("android.summaryText");
    var isGroupChat = room != null;
    if (room == null) 
      room = sender;
    var replier = new com.xfl.msgbot.script.api.legacy.SessionCacheReplier(packageName, action, room, false, "");
    var icon = bundle.getParcelableArray("android.messages")[0].get("sender_person").getIcon().getBitmap();
    var image = bundle.getBundle("android.wearable.EXTENSIONS");
    if (image != null) 
      image = image.getParcelable("background");
    var imageDB = new com.xfl.msgbot.script.api.legacy.ImageDB(icon, image);
    com.xfl.msgbot.application.service.NotificationListener.Companion.setSession(packageName, room, action);
    if (this.hasOwnProperty("responseFix")) {
      responseFix(room, msg, sender, isGroupChat, replier, imageDB, packageName, userId != 0);
    }
  }
}
let timer;
let timerTask;
const rooms = ['//🔥 로블록스 종합게임 소통방\\💫킹피스💫[게딱이벤트]\\💫 입양하세요 💫\\💎나냥이가 운영하는 타타디 토타디 토레디 킹피방💎\\👑킹피스👑[브섭제공]\\🐲 킹피스 & 킹레거시 🦀곧 게딱이벤🦀] 🐲\\🎄킹피스🎄\\🌕킹피스🪐\\🌈루카스의 종합게임 소통방🌈\\▫️킹피스▫️\\#토타디 #타타디 #애디펜 #뱅가드 #라이벌 #펫츠고\\#킹피스 #블피 #킹레거시 #블록스피스 2기\\#킹피스 #블피 #킹레거시 #블록스피스\\#킹피스 [게딱이벤트]\\[킹피 블피 브섭제공] 킹피스/블피 거래 소통방\\뱅가드 & 애라스 & 리본 🎖\\종합방 펫심99 입양하세요 아일랜드 블레이드볼\\로블록스 애니메 뱅가드/디펜더즈\\로블록스 블피 킹피스 소통방\\렌보의 어유타,너기묘방(50명마다 이벤트)\\🦋 킹피스 소통방 🦋\\🔴매일 카도 100마리🔴킹피스 카도작 & 레이드\\🦀 #킹피스 #입양하세요 소통방🦀(게딱이벤트)\\🔱•로블록스 종합 채팅방•🔱 |그랜드 그룹 서버|\\🔥킹피스👑\\🔮 킹피스 🔮\\🔥입양하세요🔥\\🔥 토타디 🔥\\킹피,블피 소통방🐉\\킹레거시 소통방\\입양하세요\\이벤트방\\애어쳐&애라스&킹피스\\애니메 뱅가드&디펜더스\\애니메 디펜더스 & 뱅가드 & 토레디 & 타타디 종합방\\종합게임방 토타디 타타디 토레디 환영아이원\\블피 킹피 소통방🤍\\블피 소통•레이드방 【브섭제공】\\블피\\블록스피스/킹피스  [브섭 제공]\\킹피스 🧡\\킹피스 🔥\\킹피스 💛\\킹피스 🌟\\킹피스 🌙\\킹피스 🌊\\킹피스 ❤️‍🔥\\킹피스 ❤️\\킹피스 ☄️\\킹피스 &  로블록스 종합소통방 《들어오면 게딱지🦀》\\킹피스\\킹피/블피 소통,거래방\\킹피스🎃\\킹피스🌌\\킹피스✨\\킹피스&토타디방 [문무제+게딱이벤트]\\킹피스/라이벌/소통방🎁\\킹피스『패왕 이벤중』\\킹피스[!!이벤트&브섭 제공!!]\\킹피스,블록스피스 🤪\\킹피스 소통방🐻\\킹피스 소통방❤️‍🔥\\킹피스 소통방 ✨️\\𝐒𝐇 𝐂𝐑𝐄𝐖 [𝙒𝘽, 𝙋𝙈 연합]\\토타디&타타디&킹피스&블피\\타타디 토타디 토레디 킹피스\\킹피스💧\\킹피스👑'];//글 보낼방 추가하고 수정하세요
const second = 1800;//몇 초 마다 보낼건지 수정하세요
const message = ["🦀 오늘 게딱 이벤 🦀🔥 오늘 용무 이벤 🔥 🦀 오늘 게딱 이벤 🦀🔥 오늘 용무 이벤 🔥 🦀 오늘 게딱 이벤 🦀🔥 오늘 용무 이벤 🔥 🦀 오늘 게딱 이벤 🦀🔥 오늘 용무 이벤 🔥 🦀 오늘 게딱 이벤 🦀🔥 오늘 용무 이벤 🔥https://open.kakao.com/o/gIeaue1g"];
function responseFix(room, msg, sender, isGroupChat, replier, imageDB, packageName) {
  if (timer == undefined) {
    timer = new java.util.Timer();
    timerTask = new java.util.TimerTask({
  run: function() {
  rooms.map(x => Api.replyRoom(x, message));
  java.lang.Thread.sleep(second * 1000);
}});
    timer.schedule(timerTask, 0, 1);
  }
}
function onStartCompile() {
  if (timer != undefined) 
    timer.cancel();
}
