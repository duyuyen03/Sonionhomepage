<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Notification Admin</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>

  <div class="container">
    <h1>Notification Admin</h1>

    <!-- STEP 1 -->
    <div id="step1">
      <h3>Select Notification Type</h3>

      <label>
        <input type="radio" name="type" value="broadcast" />
        Broadcast Notification (Toàn bộ)
      </label>

      <label>
        <input type="radio" name="type" value="targeted" />
        Targeted Notification (Theo nhóm)
      </label>

      <button onclick="goNext()">Continue</button>
    </div>

    <!-- STEP 2: BROADCAST -->
    <div id="broadcastForm" class="hidden">
      <h3>Broadcast Notification</h3>

      <input type="text" id="b_title" placeholder="Title" />
      <textarea id="b_content" placeholder="Content"></textarea>

      <button onclick="submitBroadcast()">Send</button>
    </div>

    <!-- STEP 2: TARGETED -->
    <div id="targetedForm" class="hidden">
      <h3>Targeted Notification</h3>

      <input type="text" id="t_title" placeholder="Title" />
      <textarea id="t_content" placeholder="Content"></textarea>

      <input type="file" id="excelFile" />

      <button onclick="submitTargeted()">Send</button>
    </div>

    <p id="status"></p>
  </div>

  <script src="script.js"></script>
</body>
</html>
