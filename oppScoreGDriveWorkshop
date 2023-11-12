// Add functions to Google Sheets menu
function onOpen(e) {
  // Add a custom menu to the spreadsheet.
  SpreadsheetApp.getUi() // Or DocumentApp, SlidesApp, or FormApp.
      .createMenu('OppScore Workshop...')
      .addItem('Start data collection', 'createForm')
      .addItem('Finish data collection', 'resetForm')
      .addToUi();
}

// createForms() generates the form from a list of outcomes
// in a sheet called "Outcomes".
// Only put outcome statements in the sheet, nothing else
function createForm() {
  var date = Utilities.formatDate(new Date(), "GMT+1", "yyyy-MM-dd");
  var spreadsheet = SpreadsheetApp.getActiveSpreadsheet();
  var form = FormApp.create("Opportunity Score Workshop - " + date);
  form.setDestination(FormApp.DestinationType.SPREADSHEET, spreadsheet.getId());
  form.setShowLinkToRespondAgain(false);

  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName('Outcomes');  
  //var data = sheet.getActiveRange().getValues();
  var data = sheet.getDataRange().getValues();
  var outcomeResponseArray = [];
  var impsatResponseArray = [];
  for (var i = 0; i < data.length; i++)  {
    var outcome = data[i][0]
    var section = form.addPageBreakItem().setTitle(data[i]);
    var item = form.addScaleItem();
    item.setTitle("How important is [" + outcome + "] for you?")
    .setBounds(1, 5).setLabels("Not at all important", "Extremely important").setRequired(true);
    
    var item = form.addScaleItem();
    item.setTitle("How satisfied are you with your current solution when [" + outcome + "]?")
    .setBounds(1, 5).setLabels("Not at all satisfied", "Extremely satisfied").setRequired(true);
    outcomeResponseArray.push(outcome);
    outcomeResponseArray.push(outcome);
    impsatResponseArray.push("importance");
    impsatResponseArray.push("satisfaction");
  }

  var responsesSheet = ss.insertSheet('Responses');
  responsesSheet.appendRow(impsatResponseArray);
  responsesSheet.appendRow(outcomeResponseArray);

  Logger.log('Published URL: ' + form.getPublishedUrl());
  Logger.log('Editor URL: ' + form.getEditUrl());
  var url = form.getPublishedUrl();
  displayLink(url);
}

// resetForm() disconnects the form from the spreadsheet
// Only use after response collection is done.
// (before starting analysis)
function resetForm() {
  ss = SpreadsheetApp.getActiveSpreadsheet();
  let sheets = ss.getSheets();
  var destination = ss.getSheetByName("Responses")
  var source = null;
  for (let sheet of sheets) {
    let sheetName = sheet.getName();
    let formUrl = sheet.getFormUrl();
    if (formUrl && sheetName.includes("Form responses")) {
      source = sheet;
      Logger.log("formid1 %s", FormApp.openByUrl(formUrl).getId());
    }
  }
  if(source !== null) {
    var sourceData = source.getRange(2,2,100,100);
    // get destination range
    var destinationData = destination.getRange("A3");
    // copy values to destination range
    sourceData.copyTo(destinationData);
  }
  var formURL = SpreadsheetApp.getActiveSpreadsheet().getFormUrl();
  var form = FormApp.openByUrl(formURL);
  FormApp.openByUrl(formURL).removeDestination();
  var formID = form.getId();
  DriveApp.getFileById(formID).setTrashed(true);
  ss.deleteSheet(source);
  var date = Utilities.formatDate(new Date(), "GMT+1", "yyyy-MM-dd hh:mm:ss");
  destination.setName("Responses " + date);
}

// displayLink() provides links to form for distribution
// - link to a QR code
// - link to form
function displayLink (url) {
  var qrapi = "https://api.qrserver.com/v1/create-qr-code/?size=800x800&data=";
  var html = '<html><body><a href="'+
              qrapi + url +
              '" target="blank">QR Code</a><br />' +
              '<a href="'+ url +
              '" target="blank">Form</a>' +
              '</body></html>';
  var ui = HtmlService.createHtmlOutput(html)
  SpreadsheetApp.getUi().showModelessDialog(ui,"Form Link");
}
