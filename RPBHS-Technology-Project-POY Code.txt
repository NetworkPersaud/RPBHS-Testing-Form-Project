  //Start

function afterFormSubmit(e) {
  var info = e.namedValues;
  var entryRow = e.range.getRow();
  var rowData = entryRow;
  const pdfFile = createPDF(info);
//Function that assigns the Data

  SpreadsheetApp.getActiveSpreadsheet().getSheetByName("Form Responses 1").getRange(entryRow, 27).setValue(pdfFile.getUrl());
  sendEmail(e.namedValues['Personal Email Address (Not School Email)'][0],pdfFile);
//Sends the email of the pdf to the location
  
}



function sendEmail(email,pdfFile){

  GmailApp.sendEmail(email, "Hello", "Here is your exam schdule attached as a PDF.",{
    attachments: [pdfFile],
    name: 'Automatic Emailer Script'
//Information for the email
});

}

function createPDF(info,){

//Creates the pdf

  const pdfFolder = DriveApp.getFolderById("1leUWnMckpqQNUjShr-9C7xOjutspSBS");
  const tempFolder = DriveApp.getFolderById("1qFTS47qqv6pvwZoSkFaj5RTWX3cf0Xc");
  const templateDoc = DriveApp.getFileById("1C87wuQOpwy3_yAt1jFbWxMLdAEWmQWXrY1T_6bHlRp");

  const newTempFile = templateDoc.makeCopy(tempFolder);
//Makes copy of templete

  const openDoc = DocumentApp.openById(newTempFile.getId());
  const body = openDoc.getBody();
  body.replaceText("{fn}", info['First Name'][0]);
  body.replaceText("{ln}", info['Last Name'][0]);
  body.replaceText("{gl}", info['Grade Level'][0]);
  body.replaceText("{sn}", info['Student Number'][0]);
  body.replaceText("{al}", info['AICE Exams Taking'][0]);
  body.replaceText("{ap}", info['AP Exams Taking'][0]);
//Assigns it information


  openDoc.saveAndClose();
  
  var FileName;
  FileName = info['First Name'] + info['Last Name'];
//Names PDF file

  const blobPDF = newTempFile.getAs(MimeType.PDF);
  const pdfFile = pdfFolder.createFile(blobPDF).setName(FileName + ".pdf");

tempFolder.removeFile(newTempFile);
//Removes templete docs file copy

return pdfFile;
//Returns the pdf to the email

}
//End