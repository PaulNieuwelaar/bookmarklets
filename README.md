# Dynamics 365 Bookmarklets

[View all the bookmarklets on a convenient webpage](https://paulnieuwelaar.github.io/bookmarklets/bookmarklets.html)

![bookmarklets](https://user-images.githubusercontent.com/14048382/30012692-f465787c-9195-11e7-8cc8-97d3c57f6174.png)

## FORM
**Save**  
Saves the form. For when you forget there's no save button.
```javascript
javascript:$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow.Xrm.Page.data.entity.save();
```

**Save & Close**  
Saves and closes the form, and takes you back to where you came from.
```javascript
javascript:$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow.Xrm.Page.data.entity.save('saveandclose');
```

**Save & New**  
The old save and new, perfect for creating related records in the context of a parent.
```javascript
javascript:$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow.Xrm.Page.data.entity.save('saveandnew');
```

**Refresh**  
Refreshes the data for the current record without refreshing the whole form. Great for when background processes update the record.
```javascript
javascript:$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow.Xrm.Page.data.refresh();
```

**Properties**  
Opens the old form properties for the record, showing who last modified, who created, etc.
```javascript
javascript:var frame=$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') });var id=frame[0].contentWindow.Xrm.Page.data.entity.getId();var etc=frame[0].contentWindow.Xrm.Page.context.getQueryStringParameters().etc;frame[0].contentWindow.Mscrm.RibbonActions.openFormProperties(id,etc);
```

**Open Lookup**  
Opens the selected lookup in a new window, rather than having to navigate away from the current form.
```javascript
javascript:var form=$("iframe").filter(function(){return $(this).css("visibility")=="visible"})[0].contentWindow;var elem=form.document.activeElement;var id=elem.getAttribute("id");var guid=null;var type=null;if(elem.getAttribute("isInlineLookup")=="true"){guid=elem.getAttribute("oid");type=elem.getAttribute("otypename")}else if(id!=null){var pos=id.lastIndexOf("_");if(pos>-1){var suffix=id.substring(pos+1);if(["ledit","lookupDiv","i"].indexOf(suffix)>-1){id=id.substring(0,pos)}id=id.replace("_i_ledit_multi","").replace("_ledit_multi","")}var control=form.Xrm.Page.getControl(id);if(control!=null){var field=control.getAttribute();if(field!=null){var value=field.getValue();if(value!=null){var record=value[value.length-1];guid=record.id;type=record.entityType}}}}if(guid!=null&&guid!=""&&type!=null&&type!=""){var url=form.Xrm.Page.context.getClientUrl()+"/main.aspx?etn="+type+"&id="+guid+"&pagetype=entityrecord";window.open(url)}else{alert("Unable to open record. Make sure you're clicked into a lookup field with a value.")}void(0);
```

**Open By ID**  
Opens a specific record using a given ID. Great when you get the ID from a system log and you need to open the record.
```javascript
javascript:var form=$("iframe").filter(function(){return $(this).css("visibility")=="visible"})[0].contentWindow;var type=prompt("Entity Type Name","account");if(type!=null&&type!=""){var guid=prompt("Guid","");if(guid!=null&&guid!=""){form.Xrm.Utility.openEntityForm(type,guid)}}
```

**Schemas**  
Changes all field labels to be the schema names.
```javascript
javascript:var form=$("iframe").filter(function(){return $(this).css("visibility")=="visible"})[0].contentWindow;form.Xrm.Page.ui.controls.forEach(function(a){a.setLabel(a.getName())});
```

**God Mode**  
Removes all client side validation on the form.
```javascript
javascript:var f=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;function godMode(){var a=f.Xrm.Page.data.entity.attributes.get();for(var b in a)a[b].setRequiredLevel("none"),a[b].setSubmitMode("dirty");var c=f.Xrm.Page.ui.controls.get();for(var b in c)try{c[b].setVisible(!0);c[b].setDisabled(!1);c[b].clearNotification();var d=c[b].getName();f.document.getElementById(d+"_c").style.display="";f.document.getElementById(d+"_d").style.display="";f.document.getElementById(d+"_c").parentNode.style.display=""}catch(a){}var e=f.Xrm.Page.ui.tabs.get();for(var b in e){e[b].setVisible(!0);e[b].setDisplayState("expanded");var g=e[b].sections.get();for(var b in g)g[b].setVisible(!0)}}f.Xrm.Page.data.entity.addOnSave(function(a){try{var b=a.getEventArgs();Object.keys(b).forEach(function(a){"boolean"==typeof b[a]&&(b[a]=!1)})}catch(a){}godMode()});godMode();
```

**Display Options**  
Displays all the integer values for the selected optionset.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow,elem=form.document.activeElement,id=elem.getAttribute("attrName");if(null==id&&(id=elem.getAttribute("data-attributename")),(null==id||null==form.Xrm.Page.getAttribute(id)||null==form.Xrm.Page.getAttribute(id).getOptions)&&(id=prompt("Option Set schema name:","")),null!=id){var options=form.Xrm.Page.getAttribute(id).getOptions(),text=id+":";if(null!=options&&options.length>0)for(var i=0;i<options.length;i++){var option=options[i];"null"!=option.value&&""!=option.text&&(text+="\n",text+=option.value+" - "+option.text)}alert(text)}
```

**Disable Auto Save**  
Disables the 30 second auto save for the current record/form. Allows you to test things without accidently saving.
```javascript
javascript: var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); frame[0].contentWindow.Xrm.Page.data.entity.addOnSave(function(context){ if (context.getEventArgs().getSaveMode() != 1) { context.getEventArgs().preventDefault(); } });
```

**Get Dirty Fields**  
Alerts any fields that have changed on the form. Useful when debugging why 'unsaved changes' are appearing all the time.
```javascript
javascript: var form = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow;var dirtyFields = "";var attrs=form.Xrm.Page.data.entity.attributes.get();for(var i in attrs){if(attrs[i].getIsDirty()) {if (dirtyFields != "") { dirtyFields += ", ";}dirtyFields += form.Xrm.Page.getControl(attrs[i].getName()).getLabel();}}alert(dirtyFields!=""?dirtyFields:"none");
```

**Copy a Link**  
The old copy a link we know and love.
```javascript
javascript:$("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow.Mscrm.CommandBarActions.sendFormShortcut();
```

**God Mode 2011**  
Just like God Mode, only for CRM 2011.
```javascript
javascript:var form = frames[0];var attrs=form.Xrm.Page.data.entity.attributes.get();for(var i in attrs){attrs[i].setRequiredLevel("none")}var contrs=form.Xrm.Page.ui.controls.get();for(var i in contrs){try{contrs[i].setVisible(true);contrs[i].setDisabled(false);}catch(e){}}var tabs=form.Xrm.Page.ui.tabs.get();for(var i in tabs){tabs[i].setVisible(true);tabs[i].setDisplayState("expanded");var sects=tabs[i].sections.get();for(var i in sects){sects[i].setVisible(true)}}
```

**Activate - Form**  
Activates the current record. Great for activities which typically don't allow you to reopen them.
```javascript
javascript: var form = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow;form.Mscrm.CommandBarActions.activate(form.Xrm.Page.data.entity.getId(), form.Xrm.Page.data.entity.getEntityName());
```

## VIEW
**Manage Roles**  
Allows you to assign a specific role to multiple users across multiple business units.
```javascript
javascript:var frame = document.getElementById("InlineDialog_Iframe");if (frame == null) { alert("Click Manage Roles before running the bookmarklet"); } else { var name = prompt("Security Role Name", "");var records = $("tr:contains('" + name + "')", frame.contentWindow.document).find("input"); records.prop("checked", true);alert((records != null ? records.length : 0) + " roles selected"); }void(0);
```

**Activate - Grid**  
Activates selected records from a grid. Great for activities, or user records in Dynamics 365 which don't allow you to enable.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow,recordsHtml=[];try{var form2=form.$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;recordsHtml=form2.document.getElementsByClassName("ms-crm-List-SelectedRow")}catch(e){}if(recordsHtml.length>0){for(var records=[],i=0;i<recordsHtml.length;i++){var id=recordsHtml[i].getAttribute("oid"),typeCode=recordsHtml[i].getAttribute("otype"),typeName=recordsHtml[i].getAttribute("otypename"),name=recordsHtml[i].getAttribute("oname");records.push({Id:id,Name:name,TypeCode:typeCode,TypeName:typeName})}form2.Mscrm.GridCommandActions.activate(form2.document.getElementById("crmGrid").control,records,Number(records[0].TypeCode))}
```

**Deactivate - Grid**  
Deactivates selected records from a grid. Great for activities, or user records in Dynamics 365 which don't allow you to disable.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow,recordsHtml=[];try{var form2=form.$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;recordsHtml=form2.document.getElementsByClassName("ms-crm-List-SelectedRow")}catch(e){}if(recordsHtml.length>0){for(var records=[],i=0;i<recordsHtml.length;i++){var id=recordsHtml[i].getAttribute("oid"),typeCode=recordsHtml[i].getAttribute("otype"),typeName=recordsHtml[i].getAttribute("otypename"),name=recordsHtml[i].getAttribute("oname");records.push({Id:id,Name:name,TypeCode:typeCode,TypeName:typeName})}form2.Mscrm.GridCommandActions.deactivate(form2.document.getElementById("crmGrid").control,records,Number(records[0].TypeCode))}
```

**Bulk Edit**  
Allows you to bulk edit multiple records even if Bulk Edit is disabled on your security role.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow,recordsHtml=[];try{var form2=form.$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;recordsHtml=form2.document.getElementsByClassName("ms-crm-List-SelectedRow-Lite")}catch(e){}if(0==recordsHtml.length&&(recordsHtml=form.document.getElementsByClassName("ms-crm-List-SelectedRow-Lite")),0==recordsHtml.length&&(recordsHtml=form.document.getElementsByClassName("ms-crm-List-SelectedRow")),recordsHtml.length>0){for(var records=[],i=0;i<recordsHtml.length;i++){var id=recordsHtml[i].getAttribute("oid"),typeCode=recordsHtml[i].getAttribute("otype"),typeName=recordsHtml[i].getAttribute("otypename"),name=recordsHtml[i].getAttribute("oname");records.push({Id:id,Name:name,TypeCode:typeCode,TypeName:typeName})}var actionUri=Mscrm.GridRibbonActions.$0("bulkedit",records[0].TypeCode,records.length);Mscrm.Grid.executeStandardActionInternal(actionUri,records,1e3,700)}
```

## GLOBAL
**Copy ID**  
Copy the ID's of the selected records.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow,records=[];try{records.push(form.Xrm.Page.data.entity.getId().replace("{","").replace("}",""))}catch(a){try{var recordsHtml=[],form2=form.$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;if(recordsHtml=form2.document.getElementsByClassName("ms-crm-List-SelectedRow-Lite"),0==recordsHtml.length&&(recordsHtml=form.document.getElementsByClassName("ms-crm-List-SelectedRow-Lite")),0==recordsHtml.length&&(recordsHtml=form.document.getElementsByClassName("ms-crm-List-SelectedRow")),recordsHtml.length>0)for(var i=0;i<recordsHtml.length;i++){var id=recordsHtml[i].getAttribute("oid");records.push(id.replace("{","").replace("}",""))}}catch(a){}}records.length>0?prompt("Copy the ID",records.join(",")):alert("No records selected");void(0);
```

**New**  
Takes you to the create form of a specific entity without having to find it in the sitemap or advanced find first.
```javascript
javascript:var form = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') })[0].contentWindow; try { var name = form.EntityLogicalName || form.Xrm.Page.data.entity.getEntityName(); } catch(e) { } var y = prompt('Type the schema name of the entity to create:', name ? name : 'account'); if (y) { var x = form.Xrm.Utility.openEntityForm(y); }
```

**Advanced Find**  
Simply opens advanced find. Because why not.
```javascript
javascript:var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); var form = frame[0].contentWindow; var u = form.Mscrm.CrmUri.create("/main.aspx"); u.get_query()["pagetype"] = "advancedfind"; var etc = null, viewId = null, viewType = null; try { etc = form.Xrm.Page.context.getQueryStringParameters().etc; var view = form.document.getElementById("crmGrid_SavedNewQuerySelector").childNodes[0]; viewId = view.getAttribute("currentview"); viewType = view.getAttribute("currentviewtype"); } catch(e) { } var extraqs = ""; if (etc != null) { extraqs += "EntityCode=" + etc; } if (viewId != null && viewType != null) { extraqs += "&QueryId=" + viewId + "&ViewType=" + viewType; } u.get_query()["extraqs"] = extraqs; form.openStdWin(u, "_blank", 900, 600); void (0);
```

**Create Ref Data**  
Creates a record using a given ID. Great when cloning reference data from another system.
```javascript
javascript:var form=$("iframe").filter(function(){return"visible"==$(this).css("visibility")})[0].contentWindow;try{var name=form.EntityLogicalName||form.Xrm.Page.data.entity.getEntityName()}catch(e){}var entityName=prompt("Type the schema name of the entity to create:",name||"account");if(entityName){var id=prompt("Type the ID to give the new record:","00000000-0000-0000-0000-000000000000");if(id){var req=new form.XMLHttpRequest;req.open("POST",encodeURI(form.Xrm.Page.context.getClientUrl()+"/api/data/v"+(form.APPLICATION_VERSION||"8.2")+"/"+entityName+"s"),!0),req.setRequestHeader("Accept","application/json"),req.setRequestHeader("Content-Type","application/json; charset=utf-8"),req.setRequestHeader("OData-MaxVersion","4.0"),req.setRequestHeader("OData-Version","4.0"),req.onreadystatechange=function(){if(4==this.readyState)if(req.onreadystatechange=null,this.status>=200&&this.status<=299)form.Xrm.Utility.openEntityForm(entityName,id);else{var e=JSON.parse(this.response).error;alert(e.message)}};var data={};data[entityName+"id"]=id,req.send(JSON.stringify(data))}}void(0)
```

**Mobile**  
Opens the Tablet client for the current system. Does not require IFD.
```javascript
javascript:var url = Xrm.Page.context.getClientUrl();var orgName = Xrm.Page.context.getOrgUniqueName();var baseUrl = window.location.protocol + "//" + window.location.hostname;var phoneUrl = baseUrl + "/nga/main.htm?org=" + orgName + "&server=" + encodeURIComponent(url) + "&phone=false&syncappmeta=true";window.open(phoneUrl);void(0)
```

## SETTINGS
**Customize**  
Opens the default solution.
```javascript
javascript: var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); var form = frame[0].contentWindow; try { var etc = form.Xrm.Page.context.getQueryStringParameters().etc; } catch(e) {} form.Mscrm.RibbonActions.openEntityEditor(etc);
```

**Form Editor**  
Opens the form editor for the current form or entity.
```javascript
javascript: var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); var form = frame[0].contentWindow;var etc = null;try {etc = form.Xrm.Page.context.getQueryStringParameters().etc;} catch (e) { }if (etc == null || etc == 4200) {var entity = prompt("Type the entity name", "account");if (entity != null) {etc = form.Xrm.Internal.getEntityCode(entity);if (etc == null || etc == -1) {alert("Entity '" + entity + "' does not exist");}}}if (etc != null && etc != -1 && etc != 4200) {var formId = null;try {formId = form.Xrm.Page.ui.formSelector.getCurrentItem().getId();} catch (e) { }form.Mscrm.FormEditor.OpenFormEditor(etc, "main", formId);}void(0);
```

**Publish**  
Publishes all customizations. Just in case you're not inside the solution and need to publish.
```javascript
javascript: var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); frame[0].contentWindow.Mscrm.FormEditor.PublishAll();
```

**Solutions**  
Takes you directly to the Solutions area without having to click Settings first.
```javascript
javascript: var o=new Object();o.uri="/tools/Solution/home_solution.aspx?etc=7100&sitemappath=Settings%7cCustomizations%7cnav_solution";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);
```

**Security**  
Takes you directly to the Security area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/AdminSecurity/adminsecurity_area.aspx?pagemode=iframe&sitemappath=Settings%7cSystem_Setting%7cnav_security";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Administration**  
Takes you directly to the Administration area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/Admin/admin.aspx?pagemode=iframe&sitemappath=Settings%7cSystem_Setting%7cnav_administration";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Dev Resources**  
Takes you directly to the Developer Resources area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/systemcustomization/systemCustomization.aspx?pagemode=iframe&pid=05&web=true";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Dupe Detection**  
Takes you directly to the Duplicate Detection area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/duplicatedetection/duplicatedetectionrules/home_rules.aspx";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Email Config**  
Takes you directly to the Email Configuration area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/social/social_area.aspx?pagemode=iframe&sitemappath=Settings%7cSystem_Setting%7cnav_social#";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Encryption**  
Opens the Data Encryption window so you can edit the system encryption key.
```javascript
javascript: var frame = $("iframe").filter(function () { return ($(this).css('visibility') == 'visible') }); var form = frame[0].contentWindow;form.openStdWin(form.Xrm.Page.context.getClientUrl() + "/tools/sqlencryption/sqlencryption.aspx");void(0);
```

**Imports**  
Takes you directly to the Data Imports area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/DataManagement/datamanagement.aspx?pagemode=iframe&pid=06&web=true";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**Processes**  
Takes you directly to the Processes area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/_root/homepage.aspx?etc=4703&pagemode=iframe&sitemappath=Settings%7cProcessCenter%7cnav_workflow";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**System Jobs**  
Takes you directly to the System Jobs area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/business/home_asyncoperation.aspx?pagemode=iframe&sitemappath=Settings%7cSystem_Setting%7cnav_systemjobs";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

**App for Outlook**  
Takes you directly to the App for Outlook area without having to click Settings first.
```javascript
javascript:var o=new Object();o.uri="/tools/appsforcrm/AppForOutlookAdminSettings.aspx?sitemappath=Settings%7cSystem_Setting%7ccrmapp_outlook";window.top.document.getElementById("navBar").control.raiseNavigateRequest(o);void(0);
```

Created by [Paul Nieuwelaar](http://paulnieuwelaar.wordpress.com) - [@paulnz1](https://twitter.com/paulnz1)  
Sponsored by [Magnetism Solutions - Dynamics CRM Specialists](http://www.magnetismsolutions.com)

[![](https://user-images.githubusercontent.com/14048382/30045114-3805d840-9256-11e7-9bdb-323760fb43ea.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=WDZ9MWLLWBWFE)
