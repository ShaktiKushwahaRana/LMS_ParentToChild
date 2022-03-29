# LMS_ParentToChild


We can  use lightning messaging service 
1)To Communicate between unrelated component, provided  root component is same of  both.
2)To Communicate between Parent to child.

---------------------------------------------------------
MessageChannel    folderName:messageChannels-->myChanneldemo.messageChannel-meta.xml
-----------------------------------------------------
<?xml version="1.0" encoding="UTF-8" ?>
<LightningMessageChannel xmlns="http://soap.sforce.com/2006/04/metadata">
    <masterLabel>myChanneldemo</masterLabel>
    <isExposed>true</isExposed>
    <description>Message Channel to pass a entity Id</description>
    <lightningMessageFields>
        <fieldName>myText</fieldName>
        <description>To send data from One component to anaother</description>
    </lightningMessageFields>
</LightningMessageChannel>
---------------------------------------------------------------
CHILD
--------------------------------------------------------------


<template> 
    <lightning-card title="Lightning Message Service: This is Child Component" 
                icon-name="custom:custom33">
    
                <br>
                <div style="width:200px; padding-left:50px">
                    <lightning-input class="inp" label="Enter Name" name="input1" value={input1}></lightning-input>
                    <lightning-input class="inp" label="Enter Age" name="input2" value={input2}></lightning-input>
                    <br>
                   
                        <lightning-button label="Click to subscribe" onclick={handleSubscribe} variant="brand"></lightning-button>
                   
                </div>
   
   
   
            </lightning-card>
</template> 


---------------------------------------------------------------------
import { LightningElement,track, wire } from 'lwc';
import messageChannel from '@salesforce/messageChannel/myChanneldemo__c';
import { subscribe, MessageContext } from 'lightning/messageService';

export default class Childpp extends LightningElement {

    @track input1;
    @track input2;
    @track demoObject=[];
    @wire(MessageContext)
    messageContext;
    subscription = null;

    @wire(MessageContext)
    messageContext;

    connectedCallback() {
        this.handleSubscribe();
    }

    handleSubscribe() {
        if (this.subscription) {
            return;
        }
        this.subscription = subscribe(this.messageContext, messageChannel, (message) => {
            console.log('Subscribed');
            console.log(message.myText);
            this.input1=message.myText[0];
            this.input2=message.myText[1];
        });
    }
    
}
----------------------------------------------------------------------------------
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>52.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__AppPage</target>
        <target>lightning__RecordPage</target>
        <target>lightning__HomePage</target>
        <target>lightningCommunity__Page</target>
    </targets>
</LightningComponentBundle>
--------------------------------------------------

PARENT
--------------------------------------------------------------------
<template> 
    <lightning-card title="Lightning Message Service: communication between  ParentToChild" 
                icon-name="custom:custom33">
    
                <br>
                <div style="width:200px; padding-left:50px">
                    <lightning-input class="inp" label="Enter Name" name="input1"></lightning-input>
                    <lightning-input class="inp" label="Enter Age" name="input2"></lightning-input>
                    <br>
                   
                        <lightning-button label="Click to publish" onclick={handleClick} variant="brand"></lightning-button>
                   
                </div>
   
   
   
            </lightning-card>
            <lightning-card title="CHILD Component" 
            icon-name="custom:custom33">
        
        
        <c-childpp></c-childpp>
        </lightning-card>
</template> 
----------------------------------------------------------------------------------
import { LightningElement,track,wire } from 'lwc';
import { publish, MessageContext } from 'lightning/messageService';
import messageChannel  from '@salesforce/messageChannel/myChanneldemo__c';

export default class Childpp extends LightningElement {

    @track input1;
    @track input2;
    @track myObj=[];
    @wire(MessageContext)
    messageContext;
    handleClick(event)
    {
        console.log(event.target.label);
        var inp=this.template.querySelectorAll("lightning-input");
        inp.forEach(function(element){
            if(element.name=="input1")
                this.input1=element.value;
        else if(element.name=="input2")
                this.input2=element.value;
    },this);
         this.publishMethodCall();
    }
    publishMethodCall()
    {   this.myObj[0]=this.input1;
        this.myObj[1]=this.input2;
        let message = {myText: this.myObj};
        publish(this.messageContext, messageChannel, message);
    }
}
----------------------------------------------------------------------------------

Channel Code
<?xml version="1.0" encoding="UTF-8" ?>
<LightningMessageChannel xmlns="http://soap.sforce.com/2006/04/metadata">
    <masterLabel>myChanneldemo</masterLabel>
    <isExposed>true</isExposed>
    <description>Message Channel to pass a entity Id</description>
    <lightningMessageFields>
        <fieldName>myText</fieldName>
        <description>Used For parent To child</description>
    </lightningMessageFields>
    <lightningMessageFields>
        <fieldName>myText1</fieldName>
        <description>Used for child to parent</description>
    </lightningMessageFields>
</LightningMessageChannel>
