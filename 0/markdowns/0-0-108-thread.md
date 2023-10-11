
# Post \#51815455 [Link](https://stackoverflow.com/questions/51815455/)

## how to pass data from angular material dialog to parent component?

**Vote**: 71 (93/702) **Views**: 133340 (62/702) 

**Internal ID** \#0-0-108

Created at 2018-08-13 04:59:31

Tags: `angular` `typescript` `angular-material` `angular6`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

I'm using angular 6 and I have a button which opens a dialog. in my dialog, I have a form that gets user's data and then I have two buttons to submit and cancel. I tried to show my form's data in the console but it returns undefined! whats the problem? here is part of codes:



```
import { Work } from '../../../../classes/work_shift';
import { DialogContentComponent} from './dialog-content/dialog-content.component';
export class WorkShiftsComponent implements OnInit {
 shifts: Work[];
  name: string;
  start: string;
  end: string;
  constructor(public dialog: MatDialog, private shiftService: WorkShiftsService) { }

  ngOnInit() {
  }

  openDialog() {
    const dialogRef = this.dialog.open(DialogContentComponent, {
      width: '640px',
      disableClose: true,
      data: {name: this.name, start: this.start, end: this.end}
    });
    dialogRef.afterClosed().subscribe(result => {
      console.log('The dialog was closed');
      console.log(result);//returns undefined
    });
  }
}
```




```
<mat-dialog-content>
  <form class="example-form">
    <div fxLayout="column" fxLayoutAlign="space-around" class="form">
      <div class="input">
        <mat-form-field class="input4">
          <input matInput placeholder="Shift name">
        </mat-form-field>
      </div>
      <div>
        <mat-form-field class="input input2">
          <input matInput placeholder="Start" atp-time-picker>
        </mat-form-field>
        <mat-form-field class="input input2">
          <input matInput placeholder="End" atp-time-picker >
        </mat-form-field>
      </div>
      <br/>
    </div>
  </form>
</mat-dialog-content>
<mat-dialog-actions>
  <button class="mat-button" mat-button (click)="onClose()">Cancel</button>
  <button class="mat-button" mat-button [mat-dialog-close]="data" cdkFocusInitial color="primary">Create</button>
</mat-dialog-actions>
```



----------
        
## Answer \#0

**Accepted** Vote: 19

Created at 2018-08-13 05:03:24

------------

[DEMO COMMON POP-FORM](https://stackblitz.com/edit/angular-s3kjvx)

common-pop-service:

```
import { Injectable } from '@angular/core';
import { Observable } from 'rxjs';

import { MatDialogRef, MatDialog, MatDialogConfig } from '@angular/material';
import { PupupFormComponent } from './pupup-form/pupup-form.component'

@Injectable()
export class CommonModelService {
  animal: string;
  name: string;
  date1: any;
  date2: any
  constructor(public dialog: MatDialog) { }
  openDialog(): Observable<any> {
    const dialogRef = this.dialog.open(PupupFormComponent, {
      width: '250px',
      data: { name: this.name, animal: this.animal, date1: this.date1, date2: this.date2 }
    });

    return dialogRef.afterClosed();
  }
}
```


parent.component.ts:

```
import { Component, Inject } from '@angular/core';
import { MatDialog, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material';

import { CommonModelService } from './common-model.service'

export interface DialogData {
  animal: string;
  name: string;
}

@Component({
  selector: 'dialog-overview-example',
  templateUrl: 'dialog-overview-example.html',
  styleUrls: ['dialog-overview-example.css'],
})
export class DialogOverviewExample {

  animal: string;
  name: string;

  constructor(private commModel: CommonModelService) { }

  openDialog() {
    this.commModel.openDialog().subscribe(data => {
      console.log(data);
    });
  }
}
```


parent.component.html:

```
<button mat-raised-button (click)="openDialog()">Open Form</button>
```


pup-up-form.html:

```
<div mat-dialog-content>
    <p>What's your favorite animal?</p>
    <mat-form-field>
        <input matInput [(ngModel)]="data.animal">
    </mat-form-field>

    <mat-form-field>
        <input matInput type="time" atp-time-picker [(ngModel)]="data.date1">
    </mat-form-field>

    <mat-form-field>
        <input matInput type="time" atp-time-picker [(ngModel)]="data.date2">
    </mat-form-field>
</div>

<div mat-dialog-actions>
    <button mat-button (click)="onNoClick()">No Thanks</button>
    <button mat-button [mat-dialog-close]="data" cdkFocusInitial>Ok</button>
</div>
```



------------
    
    
## Answer \#1

 Vote: 70

Created at 2019-06-16 12:37:53

------------

[Link](https://www.freakyjolly.com/angular-7-8-edit-add-delete-rows-in-material-table-with-using-dialogs-inline-row-operation/)

Just pass data back from Dialog component to parent in  method

[](https://i.stack.imgur.com/5NMlm.gif)

```
//dialog-box.component.ts
import { Component, Inject, Optional } from '@angular/core';
import { MatDialogRef, MAT_DIALOG_DATA } from '@angular/material';

export interface UsersData {
  name: string;
  id: number;
}


@Component({
  selector: 'app-dialog-box',
  templateUrl: './dialog-box.component.html',
  styleUrls: ['./dialog-box.component.css']
})
export class DialogBoxComponent {

  action:string;
  local_data:any;

  constructor(
    public dialogRef: MatDialogRef<DialogBoxComponent>,
    //@Optional() is used to prevent error if no data is passed
    @Optional() @Inject(MAT_DIALOG_DATA) public data: UsersData) {
    console.log(data);
    this.local_data = {...data};
    this.action = this.local_data.action;
  }

  doAction(){
    this.dialogRef.close({event:this.action,data:this.local_data});
  }

  closeDialog(){
    this.dialogRef.close({event:'Cancel'});
  }

}
```


Then get event & data objects/values in parent component back

```
//app.component.ts
import { Component, ViewChild } from '@angular/core';

import { MatDialog, MatTable } from '@angular/material';
import { DialogBoxComponent } from './dialog-box/dialog-box.component';

export interface UsersData {
  name: string;
  id: number;
}

const ELEMENT_DATA: UsersData[] = [
  {id: 1560608769632, name: 'Artificial Intelligence'},
  {id: 1560608796014, name: 'Machine Learning'},
  {id: 1560608787815, name: 'Robotic Process Automation'},
  {id: 1560608805101, name: 'Blockchain'}
];
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  displayedColumns: string[] = ['id', 'name', 'action'];
  dataSource = ELEMENT_DATA;

  @ViewChild(MatTable,{static:true}) table: MatTable<any>;

  constructor(public dialog: MatDialog) {}

  openDialog(action,obj) {
    obj.action = action;
    const dialogRef = this.dialog.open(DialogBoxComponent, {
      width: '250px',
      data:obj
    });

    dialogRef.afterClosed().subscribe(result => {
      if(result.event == 'Add'){
        this.addRowData(result.data);
      }else if(result.event == 'Update'){
        this.updateRowData(result.data);
      }else if(result.event == 'Delete'){
        this.deleteRowData(result.data);
      }
    });
  }

  addRowData(row_obj){
    var d = new Date();
    this.dataSource.push({
      id:d.getTime(),
      name:row_obj.name
    });
    this.table.renderRows();

  }
  updateRowData(row_obj){
    this.dataSource = this.dataSource.filter((value,key)=>{
      if(value.id == row_obj.id){
        value.name = row_obj.name;
      }
      return true;
    });
  }
  deleteRowData(row_obj){
    this.dataSource = this.dataSource.filter((value,key)=>{
      return value.id != row_obj.id;
    });
  }


}
```



------------
    
    
## Answer \#2

 Vote: 48

Created at 2020-03-05 11:29:54

------------


## Data send to and/or receive from Dialog



```
// HTML
<div [innerHTML]="data"></div>
<button (click)="cancel()">No</button>
<button (click)="confirm()">Yes</button>

// Typescript
export class DialogComponent {

  // receive data from parent using 'MAT_DIALOG_DATA'
  constructor(@Inject(MAT_DIALOG_DATA) public data: string,
    private dialogRef: MatDialogRef<DialogComponent>) { }

  cancel() {
    // closing itself and sending data to parent component
    this.dialogRef.close({ data: 'you cancelled' })
  }

  confirm() {
    // closing itself and sending data to parent component
    this.dialogRef.close({ data: 'you confirmed' })
  }

}
```


```
constructor(private dialog: MatDialog) { }

// method to open dialog
openDialog() {
    let dialogRef = this.dialog.open(DialogComponent, {
      data: `Are you sure you want to delete?`
    })

    dialogRef.afterClosed().subscribe(res => {
      // received data from dialog-component
      console.log(res.data)
    })
}
```



------------
    
    
## Answer \#3

 Vote: 10

Created at 2021-01-09 21:40:23

------------

I use EventEmitter than subscribe to save event of the dialog. A bit more work but fits better to solve my problem. Because mostly you pass an object which you modify in the Dlg and maybe you doesn't want to pass back the modification. The solution requere some kind of Deep Copy solution.
In the dialog:
```
import { EventEmitter } from '@angular/core';
@Component({...})
export class DlgComponent {
 data: any
 data: deepCopy(data) // some kindof deep copy solution like lodash
 onSave = new EventEmitter();

 ....
 save() {
     this.onSave.emit(this.data)
 }
```

In the parent component:
```
import { MatDialog, MatDialogConfig, MatDialogRef, MAT_DIALOG_DATA } from '@angular/material';

@Component({...})
export class ParentComponent {
 data: any
 
 constructor() {
   public dialogRef: MatDialogRef<DlgComponent>,
 }

 openDlg() {
   let dialogConfig = new MatDialogConfig(); 
   const dialogRef = this.dialog.open(DlgmComponent, dialogConfig);

   dialogRef.componentInstance.onSave.subscribe(data=> {
     this.data = data    
   })
 }
  
}
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-04-30 17:37:37

------------

In the dialog:
```
close(): void {
   this.dialogRef.close(data); //on close pass data to parent
  }
```

In the parent component:
```
openDialog(row){
        const dialogRef = this.dialog.open(DialogComponent, {
          width: '880px',
          height: '480px',
          disableClose: true,
          data: data
        });

        dialogRef.afterClosed().subscribe(res => {
        console.log("value from dialog",res) //data from dialog
            this.headerDialogValue = res;
            });
      }
```



------------
    
    
## Answer \#5

 Vote: 0

Created at 2022-11-29 05:29:20

------------

Below Solution works for me:
[Matdialog Sharing Data Demo](https://stackblitz.com/edit/matdialog-sharing-data-demo?file=app%2Fapp.component.ts)
[](https://i.stack.imgur.com/SMtfe.png)


------------
    
    