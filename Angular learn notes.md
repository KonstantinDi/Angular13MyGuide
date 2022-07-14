### angular cli - основные комманды для Angular cli:

ng g c componentName --skip-tests - создание нового компонента без тетстов


### Односторонний биндинг - [ ]:

Пример одностороннего биндиннга (связь html с js), то есть то html будет
изменяться тогда когда изменяеться что то в js. В данному случае в атрибут 
src будет записано то что лежит в переменной img из файла компонента .ts.


    <img [src]="img" alt="">

### Событийный биндинг event binding () 

Это механизм при котором мы можем что то делать в js, когда в html происходит событие.

    <input type="text" (keyup.enter)="onInput($event)"> 
Здесь в круглых скобках описано событие поднятие 
клавиши enter, при котором сработает описанная в компоненте функция onInput(), в 
которую передаеться специальная переменная события.

### Локальные ссылки на теги

    <input type="text"
           (keyup.enter)="onInput($event)"
           (blur)="onBlur(myInput.value)"
           #myInput>
" #myInput " - Локальная ссылка на объект тега, очень удобно передавать как парамерт функции, чем то похожа на this,
для данного элемента.

### two-way binding - двухстороннее связывание:

В модуле должен быть импортирован FormsModule, ngModel - директива для
тегов ввода даннх, отображает состояния данных в html и js синхронизируя их
тогда в html можно так 

    <input type="text" [(ngModel)]="title">
[()] - это и есть двухстороннее связывание.

### директива ngStyle и ее аналог:

Сдесь показаны 2 варианата , 
- 1 - это использовани директивы ngStyle с объектом в котором
в зависимости от значения переменной будет применен опредленный класс для тега <p>
- 2 - смысл тот же но синтаксис применения другой.


    <p [ngClass]="{
      red: backgroundToggle, // если backgroundToggle === true тогда используем класс red
      blue: !backgroundToggle} // если backgroundToggle !== true тогда используем класс blue
    >
    Teletexts
    </p>

    <p
      [class.blue]="!backgroundToggle"
      [class.red]="backgroundToggle"
    >
    Teletexts
    </p>


### Директива *ngif с else:

Это структурная директива, то есть влияющая на отображаемый html об этом говорит *, 
тут показан пример, где используеться конструкция if else

    <p *ngIf="toggle; else *ngIf="toggle; else blueP" class="red">Lorem Prosto</p> 
    /* 
    *ngIf="toggle; else blueP - эта запись означает, если toggle === true, 
    тогда показывать этот тэг <p>, в ином случае показывать тег со ссылкой blueP
    */

    <ng-template #blueP>  // #blueP это ссылка для ngIf, ng-template специальная обертка - контейнер в Angular
      <p class="blue">Lorem Lorem</p>
    </ng-template>

### Директива *ngSwitch аналог if else, только вариантов больше и удобнее в чем то.

    <button (click)="toggle = !toggle">Toggle</button>
    <button (click)="toggle = 1234">Toggle default</button>
    
    <div [ngSwitch]="toggle"> // в зависимости от совпадения условия будет показываться определенный html
      <p *ngSwitchCase="true">Text.</p> 
      <p *ngSwitchCase="false">Text.</p>
      <p *ngSwitchDefault>Text.</p>
    </div>

### Директива *ngFor дляя итерирования циклов в html

Позволяет вывести массив либо объект, а так же можно вложенныйй объект или массив вывести:

Это код с данными в компоненте

      arr = [1,21,4,52,12,45];
      objs = [
        {title: "post1",author: 'Veldolen',comments:[
            {name: "Nikol", text: "Hello where you are?"},
            {name: "Nikol", text: "Hello where you are?"},
            {name: "Nikol", text: "Hello where you are?"},
          ]
        },
        {title: "post2",author: 'Veldolen',comments:[
            {name: "Quelot", text: "Hello where you are1?"},
            {name: "Quelot", text: "Hello where you are2?"},
            {name: "Quelot", text: "Hello where you are3?"},
          ]
        }
      ]

Это код в html вариант с итерацией массива и объекта причем с двойным вложением

    <div>
      <p *ngFor="let n of arr; let idx = index">{{idx}}-{{n}}</p> 
    // показываем "индекс - значение элемента в массиве"
    </div>

    // пример с двойным вложеннием для отображения объекта в объекте
    // например есть массив с постами а в каждом посте есть массив с комментами  
    <div>
      <div *ngFor="let post of objs"> 
        <p>{{post.title}} <b>{{post.author}}</b></p>
        <div *ngFor="let comment of post.comments">
          <p>{{comment.name}}-{{comment.text}}</p>
        </div>
      </div>
    </div>

### Передача данных из компоннта родителя в компонент ребенок (вниз):
Есть компонент родитель, нужно передать параметры в компонент ребенок.

Это компонент родитель его .ts

    export interface Post { // интерфейс создает контракт описывающий объект поста
      title: string
      text: string
      id?: any
    }
    
    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
    
      posts: Post[] = [ // передаваемый массив в ребенка, точнее передаеться один пост в компонент ребенка пост
        {title: 'title1', text:'text1', id:1},
        {title: 'title2', text:'text2', id:2}
      ]
    
    }

Это html для компонента родителя:

    <div class="containter">
      <h1>Angular Components</h1>
    
      <app-post
      *ngFor="let p of posts" // цикл будет столько компонентов ребенка сколько объектов в массиве posts
      [post]="p"
      ></app-post>
    </div>

.ts для компонента ребенка , важный момент это декоратор  @Input() применяемый для приема
данных из компонента родителя в компонент ребенка

    import {Component, Input, OnInit} from '@angular/core';
    import {Post} from "../app.component";
    
    @Component({
      selector: 'app-post',
      templateUrl: './post.component.html',
      styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit {
    
      @Input() post!: Post // - декоратор нужен в angular чтобыы обозначить что это принимаемый в компонент параметр
        
        // post типа Post (Интерфейс объявленный в родительском компоненте)
    
      constructor() {}
    
      ngOnInit(): void {
      }
    
    }

html - компонента ребенка в котором используеться переменная принятая из родителя post.

    <div class="card">
      <h2>{{post.title}}</h2>
      <p>{{post.text}}</p>
      <p>{{post.id}}</p>
    </div>


### Передача данных в из компонента ребенка в родителя (наверх):

Если нужно передать данные из компонента ребенка в компонент родителя, то делаеться так:
есть форма в компоненте ребенка, в нее вводяться даннные, которые затем добавляються в массив
данных находящийся в родителе.

html компонента ребенка (два инпута, с кнопкой добавить пост)

    <div class="container">
    <input
    type="text"
    class="form-control"
    placeholder="Title..."
    [(ngModel)]="title"
    >
    <input
    type="text"
    class="form-control"
    placeholder="Text..."
    [(ngModel)]="text"
    >
    <button class="btn" (click)="addPost()">Добавить пост</button>
      <hr/>
    </div>

ts код компоннта ребенка - важная часть EventEmitter, специальный класс, 
который выполняет функцию передачи данных наверх с помощью метода emit(). Запускаем
этот метод в методе отвечающем за обрботку нажатия кнопки, и передаем в метод emit(data) перменную
с данными как параметр


    import {Component, EventEmitter, OnInit, Output} from '@angular/core';
    import {Post} from "../app.component";
    
    @Component({
    selector: 'app-post-form',
    templateUrl: './post-form.component.html',
    styleUrls: ['./post-form.component.scss']
    })
    export class PostFormComponent implements OnInit {
    
    @Output() onAdd:EventEmitter<Post> = new EventEmitter<Post>();
    
    title = '';
    text = '';
    
    constructor() {
    }
    
    ngOnInit(): void {
    }
    
    addPost(): void {
    if (this.title.trim() && this.text.trim()) {
    
          const post: Post = {
            title: this.title,
            text: this.text
    
          }
    
          this.onAdd.emit(post); // отработкав метода emit()
    
          console.log('New Post:',post)
    
          this.title = this.text = '';
        }
    }
    }

html компонента родителя:
-<app-post-form> - компонент ребенка, в нем событийный биндинг (onAdd)="updatePosts($event)" - когда
сработает метод emit в ребенке, при нажатии кнопки, директива (onAdd) поймает данные из emit(),
и передаст их методу updatePosts($event) расположенному в родителе,
который уже с этими данными что то сделает, например добавит пост в массив посотов.

    <div class="containter">
      <h1>Angular Components</h1>
      <app-post-form
        (onAdd)="updatePosts($event)"
      >
      </app-post-form>
    
    <app-post
    *ngFor="let p of posts"
    [post]="p"
    ></app-post>
    </div>

ts компонента родителя:

    import { Component } from '@angular/core';
    
    export interface Post {
    title: string
    text: string
    id?: any
    }
    
    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
    
    posts: Post[] = [
    {title: 'title1', text:'text1', id:1},
    {title: 'title2', text:'text2', id:2}
    ]
    
    updatePosts(post: Post){ // принимает данные из ребенка и добавляет их в массив
    this.posts.unshift(post);
    console.log('Post', post);
    }
    }

### Доступ до HTML улументов через #ссылку в angular

К тегу формы в input добавляем ссылку #titleInput она будет указывать
на объект этого тега.

html:

    <div>
    <input
    type="text"
    class="form-control"
    placeholder="Title..."
    [(ngModel)]="title"
    #titleInput>
    
    <button class="btn" (click)="onAddPost()">Add post</button>

    <button class="btn" (click)="focusTitle()">Focus title</button>
    </div>

Добавлен специальный декоратор @ViewChild для переменной inputRef, она содержит 
объект через который получаем доступ к объекту input для которого была поставлена
ссылка. При нажатии на кнопку, отрабатывает метод focusTitle(), который делает
фокус на input с ссылкой.

ts:

    import {Component, ElementRef, EventEmitter, OnInit, Output, ViewChild} from '@angular/core';
    import {Post} from "../app.component";
    
    @Component({
    selector: 'app-post-form',
    templateUrl: './post-form.component.html',
    styleUrls: ['./post-form.component.scss']
    })
    export class PostFormComponent implements OnInit {
    
    @Output() onAdd: EventEmitter<Post> = new EventEmitter<Post>();
    
    @ViewChild('titleInput',{static: false}) inputRef!: ElementRef
    
    title = '';
    text = '';
    
    constructor() { }
    
    ngOnInit(): void {
    }
    
    onAddPost(){
    if(this.text.trim() && this.title.trim()){
    const post: Post = {
    title: this.title,
    text: this.text
    }
    
          this.onAdd.emit(post);
    
          this.title = this.text = '';
        }
    }
    
    focusTitle(){
    this.inputRef.nativeElement.focus();
    }
    
    }

### Обращение к html внутри компонента
Например имеем внутри тегов компонента HTML - так тоже можно и с 
этим html тоже можно работать. Чтобы он отображался во первых нужно, внутри html
самого компонента указать, куда вставлять этот HTML с помощью спец тега 
<ng-content></ng-content>. вот так:

Это компонент родителя , внутри него компонент ребенка app-post внутри него html
хотим к нему обратиться, делаем тег обертку со ссылкой #info

    <app-post *ngFor="let p of posts" [post]="p">
    
        <div #info>
          <small *ngIf="p.text.length > 2; else short">Post long</small>
          <ng-template #short>Post short</ng-template>
        </div>
    
    </app-post>

html компонента ребенка: 

    <div class="card">
    <h2>{{post.title}}</h2>
    <p>{{post.text}}</p>
    <ng-content></ng-content> // сюда вставаиться html  <app-post>HTML<app-post/>
    </div>

Для работы с этим html в ts файле комонента ребенка будет использоваться
декоратор @ContentChild:

    import {Component, ElementRef, Input, OnInit, ContentChild} from '@angular/core';
    import {Post} from "../app.component";
    
    @Component({
    selector: 'app-post',
    templateUrl: './post.component.html',
    styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit {
    
    @Input() post!: Post
    @ContentChild ('info', {static: true}) infoRef!: ElementRef
    
    
    constructor() { }
    
    ngOnInit(): void {
    console.log(this.infoRef.nativeElement); // получаем объект html вставленного 
    //внутрь компонента ребенка.
    }
    
    }
Замечание: когда используем декоратор для html ребенка через ссылку #, то
указываем {static: true} - если хотим использовать переменную напримре infoRef,
внутри метода ngOnInit(), если же снаружи его, тогда нужно {static: false}


### Life cycle hook Angular - хуки жизненного цикла 
Набор хуков, пояснение в комментарии к каждому хуку

    import {
    Component,
    ElementRef,
    Input,
    OnInit,
    ContentChild,
    OnChanges,
    SimpleChanges,
    DoCheck,
    AfterContentInit, AfterContentChecked, AfterViewInit, AfterViewChecked, OnDestroy, Output, EventEmitter
    } from '@angular/core';
    import {Post} from "../app.component";

    @Component({
    selector: 'app-post',
    templateUrl: './post.component.html',
    styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements
    OnChanges,
    OnInit,
    DoCheck,
    AfterContentInit,
    AfterContentChecked,
    AfterViewInit,
    AfterViewChecked,
    OnDestroy
    {

    @Input() post!: Post
    @Output() onRemove = new EventEmitter<number>(); // для удаления поста
    @ContentChild ('info', {static: true}) infoRef!: ElementRef

    constructor() { }

    removePost(){
    this.onRemove.emit(this.post.id); /* эммитит в верхний компонент, в котором данный компонент используеться
    айдишник поста, который нужно удалить.*/
    }

    /* Данный метод отрабатывает, когда Angular переустанавилвает забинденные свойства, метод
    содержит объект, в котором записаны текущие и прошлые значения этих свойств*/
    
      ngOnChanges(changes: SimpleChanges): void {
      console.log('ngOnChanges: ', changes );
      }

    /* Запускаеться при инициализации компонента/директивы, после того как Angular отобразит свойства связанные
    с данными компонента, и установит входные свойства директивы или компонента */
      ngOnInit(): void {
      console.log('ngOnInit');
      // console.log(this.infoRef.nativeElement);
      }

    /* Вызывается сразу после ngOnChanges() при каждом запуске обнаружения изменений и сразу после ngOnInit()
    при первом запуске. Используеться для проверок данных н что либо */
    ngDoCheck(): void {
    console.log('DoCheck');
    }

    /* Вызываеться тогда когда инициализируеться контент который передается в компонент
    Под контентом имееться ввиду html внутри компонента, то есть <component>ВОТ ЭТОТ HTML<component/> */
      ngAfterContentInit(): void {
      console.log('ngAfterContentInit');
      }

    /* Вызываеться тогда когда весь контент который передаем готов для использования */
    ngAfterContentChecked(): void {
    console.log('ngAfterContentChecked');
    }

    /* Вызываеться когда вначале мы инициализируем html контент который передаем в компонент, 
    затем уже инициализируеться html - ( view - вид) внутри самого компонента */
      ngAfterViewInit(): void {
      console.log('ngAfterViewInit')
      }
      /* аналогичной ngDoCheck только для вида компонента */
      ngAfterViewChecked(): void{
      console.log('AfterViewChecked');
      }

    /* будет вызван когда компонент будет уничтожен, в этом методе можно отписываться от 
    Observable, event handlers чтобы избежать утечки памяти*/
      ngOnDestroy() {
      console.log('ngOnDestroy')
      }
    
    }

Последний хук ngOnDestroy() - пример использования:

Будем удалять компонент поста выводимого в компонте приложения.

1. Есть компонент ребенка, в его html, есть кнопка, при нажатии на которую будет передаваться
айдишник поста, "вверх" в компонент родителя.

html компонента ребенка с этой кнопкой:

    <div class="card">
      <h2>{{post.title}}</h2>
      <p>{{post.text}}</p>
      <button class="btn" (click)="removePost()">&times;</button> // кнопка
    
    <ng-content></ng-content>
    </div>

Самописный метод removePost() в ts ребенка в классе.

    import {Component,ElementRef,OnInit,Output, EventEmitter} from '@angular/core';
    import {Post} from "../app.component";
    
    @Component({
    selector: 'app-post',
    templateUrl: './post.component.html',
    styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit{

    @Output() onRemove = new EventEmitter<number>(); // для удаления поста принимает число
    
    constructor() { }
    
    removePost(){
    this.onRemove.emit(this.post.id); /* эммитит в верхний компонент, в котором данный компонент используеться
    айдишник поста, который нужно удалить.*/
    }

2. Есть компонент родителя, в нем выводяться компоненты посты:
Биндинг onRemove вызывает метод удаления поста у самого родителя, название совпадает
с названием переменной эммитера/onRemove в компоненте ребенка.


    <app-post *ngFor="let p of posts" [post]="p" (onRemove)="removePost($event)">
    
        <div #info>
          <small *ngIf="p.text.length > 2; else short">Post long</small>
          <ng-template #short>Post short</ng-template>
        </div>

    </app-post>

ts в родетеле:

    import { Component } from '@angular/core';
    
    export interface Post {
        title: string
        text: string
        id?: number
    }
        
        
    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
        posts: Post[] = [
        {title: 'title1',text: 'text1',id: 1},
        {title: 'title2',text: 'text2',id: 2},
        // {title: 'title3',text: 'text3',id: 3},
        ]
        
        updatePosts(post: Post){
        this.posts.unshift(post);
        }
        /* метод "удаляющий" компонент, конечно он удаляет данные для 
        формирования компонента и тем самым удаляеться компонент и срабатывает хуук 
        ngOnDestroy() содержащийся в удаляемом компоненте */

        removePost(id: number){
        this.posts = this.posts.filter(p => p.id !== id)  
        }
    }

### Change detection strategy - Изменение стратегии отслеживания обновления данных в компонентах

Что это такое: в ангуляре можно прям в основной компонент в метод onInit() записать код
по изменению данных. И дело в том что ангуляр следит за всеми данными в компонентах и 
при их изменении он заного их отображает. Если компонтов много, то все это дело может
начать тормозить. Что делать?
Можно прописать другую стратегию отслеживания изменения этих данных в компонентах,
выглядит это все так:

Есть родительский основной компонент, в нем пропишем метод onInit()
который изменяет данные.

    import {Component, OnInit} from '@angular/core';

    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss']
    })
    export class AppComponent implements OnInit{
        posts: Post[] = [
        {title: 'title1',text: 'text1',id: 1},
        {title: 'title2',text: 'text2',id: 2},
        // {title: 'title3',text: 'text3',id: 3},
        ]

        ngOnInit(): void {
            setTimeout(()=>{
            console.log('Timeout');
            this.posts[0].title = 'Changed';
            },2000)
        }

    }

Сейчас мы изменяем одну запись в массиве posts, ссылка на первый объект в массиве не
меняеться.То есть Angular отслеживает данные в этом массиве. 

Теперь поменяем эту стратегию:
Напишев в компоненте поста следующее.

    // code before...
    @Component({
    selector: 'app-post',
    templateUrl: './post.component.html',
    styleUrls: ['./post.component.scss'],
    // изменяем сдесь стратегию отслеживания изменения данных в компоненте на
    changeDetection: ChangeDetectionStrategy.OnPush 
    })
    export class PostComponent implements{
    //code after...

Важно понимать что сами данные в родительском элементе будут меняться с помощью кода
написанного в onInit, но Angular будет пропускать это "мимо" и не будет отрисовывать.
Если же мы поменяем код так чтобы менялась ссылка на весь объект в массиве, то есть весь
компонент. Тогда данные будут отрисовываться.

    export class AppComponent  implements OnInit{
        posts: Post[] = [
        {title: 'title1',text: 'text1',id: 1},
        {title: 'title2',text: 'text2',id: 2},
        // {title: 'title3',text: 'text3',id: 3},
        ]
    
        ngOnInit(): void {
            setTimeout(()=>{

            console.log('Timeout');
            this.posts[0] = {title:'Changed', text: 'Changed text', id: 2};
            console.log(this.posts[0].title);

            },2000)
        }
    }

### Изменение поведения стилей компонентов.

В Angular все стили по дефолту локализированны к своим компонентам, для этого в теги html
компонента добавляются атрибуты с хешем. То есть если задать тегу h2 компонента ребенка
стиль color: red, то на родительском теге h2 это никак не отразиться, и наоборот так же.
Но это поведение можно изменить если добавить в декоратор класса компонента строку:
encapsulation: ViewEncapsulation.None

    @Component({
    selector: 'app-post',
    templateUrl: './post.component.html',
    styleUrls: ['./post.component.scss'],
    changeDetection: ChangeDetectionStrategy.OnPush,
    encapsulation: ViewEncapsulation.None
    })
    export class PostComponent implements

Теперь стили из этого класса-компонента будут глобальными как для родительских 
компонентов так и для дочерних.

### Создание собственных директив

Создаем папку directives в папке app, тут будет новая директива style.directive.ts.
Благодаря такому названию файлы, Angular подскажет нам название класса:

    import {Directive} from "@angular/core";
    
    @Directive({
    selector: '[appStyle]' /* это селектор, чтобы потом использовать директиву по 
                            ее названию, квадратные скобочки здесь, чтобы Angular 
                            понимал что это директива, а не компонент, то есть чтобы
                            ее использовать нужно данное название прописать как атрибут
                            тега, а не сам тег.*/
    })
    export class StyleDirective {
    
    }

Не забудьте добавить эту директиву в app.modules.ts

    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    
    import { AppComponent } from './app.component';
    import { FormsModule } from "@angular/forms";
    import { StyleDirective } from "./directives/style.directive";
    
    @NgModule({
    declarations: [
    AppComponent,
    StyleDirective // добавлена своя директива
    ],
    imports: [
    BrowserModule,
    FormsModule
    ],
    providers: [],
    bootstrap: [AppComponent]
    })
    export class AppModule { }

Теперь используем эту директиву в html основного компонента так:

    <div class="container">
      <h1>Angular Components</h1>
      <p appStyle>Test text for directive </p> 
    </div>

#### Быстрое создание директив

Чтобы быстро создать директиву, нужно просто прописать в консоле:

    ng generate directive style2 --skip-tests 

или кратко

    ng g d folder/style2 --skip-tests это еще и указав в какой папке ее создать


#### Получение доступа к элементу в директиве

В данном случае мы получаем доступ к элементу который использует директиву с помощью
Dependency injection в конструкторе, нам нужны два атрибуты это сам элемент
private el: ElementRef и private r:Renderer2 - то специальный контейнер, для кросплат
форменного использования, можно и без него обращаться  el:ElementRef, но есть риск
что на мобилках будет ошибка.
Теперь меняем стили у тега который использует эту директиву appStyle, с помощью
метода setStyle у Renderer2, положив туда сам элемент, и выбрав стиль и его значение.

    import {Directive, ElementRef, Renderer2} from "@angular/core";
        
        @Directive({
        selector: '[appStyle]'
        })
        export class StyleDirective {
        constructor(private el: ElementRef,private r:Renderer2) {
        this.r.setStyle(this.el.nativeElement,'color','green');
        }
    }

Заметка: важно что использование двух этих di "private el: ElementRef,private r:Renderer2"
позволяет коду быть кроссплатформенным.

#### Добавление декораторов обработки событий для директив

Для того чтобы добавить обработчки событя используеться декоратор @HostListener, ниже
пример с комментариями как это выглядит.

    import {Directive, ElementRef, HostListener, Renderer2} from "@angular/core";
    
    @Directive({
    selector: '[appStyle]'
    })
    export class StyleDirective {
        constructor(private el: ElementRef,private r:Renderer2) {
        // this.r.setStyle(this.el.nativeElement,'color','green'); // меняем стиль элемента
        }
        
        @HostListener('click',['$event.target']) // декоратор для события клинка на элемент
        onClick(event: Event){
        console.log(event);
        }
        
        @HostListener('mouseenter') // декоратор для события клинка на элемент
        onEnter(){
        this.r.setStyle(this.el.nativeElement, 'color','red'); // смена стиля при наведени мыши
        }
        
        @HostListener('mouseleave') // декоратор для события клинка на элемент
        onLeave(){
        this.r.setStyle(this.el.nativeElement, 'color',null); // смена стиля при уведении мыши
        }
    
    }

#### Передача параметров в директиву

 В директиву так же можно передавать параметры как и в компоненты, это делаеться похожим
образом, для этого необходимо в самой дерективе добавить декоратор для свойств @Input

    @Directive({
    selector: '[appStyle]'
    })
    export class StyleDirective {
    @Input() fontColor: string = 'yellow'
    @Input() fontWeight: string = 'normal'

затем в html где применяеться директива

    <p appStyle [fontColor]="'brown'" [fontWeight]="'600'">Test text for directive </p>

Следует обратить внимание что синтаксис в html для передачи параметров может быть и таким

    <p appStyle fontColor="brown" fontWeight="600">Test text for directive </p>
Но сдесь мы говорим что атрибуты fontColor и fontWeight могутбыть только строковывми,
в любом случае лучше применять 1 тип синтаксиса, так как он одноврменно означает
что данные атрибуты являються биндингами, во втором же случае это не понятно.

Давайте теперь передадим как параметр директивы объект:

ts код директивы: 

    import {Directive, ElementRef, HostListener, Input, Renderer2} from "@angular/core";
    
    @Directive({
    selector: '[appStyle]'
    })
    export class StyleDirective {
        @Input() fontColor: string = 'yellow'
        @Input() dStyles!: {border?: string, fontWeight?: string, borderRadius?: string}
        
        constructor(private el: ElementRef,private r:Renderer2) {
        }
        
        @HostListener('mouseenter')
        onEnter(){
            this.r.setStyle(this.el.nativeElement, 'font-weight', this.dStyles.fontWeight)
            this.r.setStyle(this.el.nativeElement, 'color', this.fontColor);
            this.r.setStyle(this.el.nativeElement, 'border', this.dStyles.border);
            this.r.setStyle(this.el.nativeElement, 'borderRadius', this.dStyles.borderRadius);
        }
        
        @HostListener('mouseleave')
        onLeave(){
            this.r.setStyle(this.el.nativeElement, 'font-weight',null);
            this.r.setStyle(this.el.nativeElement, 'color',null);
            this.r.setStyle(this.el.nativeElement, 'border', null);
            this.r.setStyle(this.el.nativeElement, 'border-radius', null);
        
        }
    }

html применения директивы:

    <div class="container">
      <h1>Angular Components</h1>
      <p appStyle
         [dStyles]="{border: '1px solid blue',fontWeight: '600', borderRadius: '20px'}"
         [fontColor]="'brown'">
        Test text for directive
      </p>
    
      <p appStyle
         [dStyles]="{border: '2px solid green',fontWeight: '500', borderRadius: '5px'}"
         [fontColor]="'yellow'">
        Test text for directive
      </p>
    </div>

#### Декоратор HostBinding применение для изменения стилей в директиве

Данный декоратор можно применять в директиве, для более удобного управления стилями
и не только стилями, со стилями это выглядит так:

html применения директивы:

    <div class="container">
      <h1>Angular Components</h1>
      <p appStyle
         [fontColor]="'brown'">
        Test text for directive
      </p>
    
      <p appStyle
         [fontColor]="'yellow'">
        Test text for directive
      </p>
    </div>

ts с этим декоратором

    import {Directive, ElementRef, HostBinding, HostListener, Input, Renderer2} from "@angular/core";
    
    @Directive({
    selector: '[appStyle]'
    })
    export class StyleDirective {
        @Input() fontColor = 'blue';
        
        @HostBinding('style.color') elColor = '';
        
        constructor(private el: ElementRef,private r:Renderer2) {
        }
        
        @HostListener('mouseenter')
        onEnter(){
            this.elColor = this.fontColor;
        }
        
        @HostListener('mouseleave')
        onLeave(){
            this.elColor = '';
        }
    }

Для декоратора @HostBinding указываеться параметр style.color(можно и другой) который 
будет изменяться для всей дерективы и привязывает к нему переменную elColor, 
в эту переменную при наступлении определенного события кладется значение 
атрибута переданного с помощью декоратора @Input внутрь дерективы и у элемента
с этой дерективой стили меняються.

#### Создание структурных директив

В данному примере будет создаваться директив похожая на *ngIf для этого создадим новую
директиву введя в терминале ng g d directives\ifNot --skip-tests это создаст заготовку
директивы, затем отредактируем ее вот так:

ts директивы:


    import {Directive, Input, TemplateRef, ViewContainerRef, ViewRef} from '@angular/core';
    
    @Directive({
    selector: '[appIfnot]'
    })
    export class IfnotDirective {
    
        @Input('appIfnot') set ifNot(condition: boolean){
            if (!condition){
                // показать элементы
                this.viewContainer.createEmbeddedView(this.templateRef)
            } else {
                // скрыть элементы
                this.viewContainer.clear();
            }
        }
        
        // templateRef содержит html блока к которому применена директива
        // ViewContainerRef содержит ссылку на контейнер
        constructor(private templateRef: TemplateRef<any>,
        private viewContainer: ViewContainerRef) { }
    
    }

html для использования нашей структурной директивы его нужно прописать вутри html 
родительского компонента, тут два способа написания html, один со '*' другой без, c помощью
'*' angular под капотом создает <ng-template> и как бы делает html как во 2 способе.

    <button class="btn" (click)="isVisible = !isVisible">Touch it</button>
    
    // 1способ
    <div class="wrap" *appIfnot="!isVisible">
        <p>Texsst text</p>
    </div>
    
    // 2 способ
    <ng-template [appIfnot]="!isVisible">
        <div class="wrap" *appIfnot="!isVisible">
            <p>Texsst text</p>
        </div>
    </ng-template>

## Пайпы
Это механизмы позволяющие изменить отображение данных

### Пайпы. Работа с числами


    // было число 2,784644 станет:
    <div class="container">
      <h1>Angular Components</h1>
      <p>{{e | number:'1.1-5'}}</p> // 2.78465
      <p>{{e | number:'3.1-2'}}</p> // 002.79
      <p>{{e | number:'1.0-0'}}</p> // 3
    </div>

### Пайпы. Работа со строками

    <p>{{str}}</p> // str = 'test str';
    <p>{{str | uppercase  }}</p > // выведет TEST STR
    <p>{{str | lowercase }}</p> // выведет test str
    <p>{{str | uppercase | lowercase }}</p> // выведет test str
    <p>{{str | titlecase }}</p> // выведет Test Str
    <p>{{str | titlecase | slice: 1}}</p> // отрежет 1 символ выведет est Str
    
    // выведет est , сначало вырежет 1 символ потом дойдет до 5 индекса и отрежет хвост
    <p>{{str | titlecase | slice: 1:5}}</p> 

### Пайпы. Даты

    // для отображения нужного формата даты смотри документацию

    <p>{{date | date: 'longDate'}}</p> // May 25, 2022
    <p>{{date | date: 'dd-mm-YYYY'}}</p> // 25-09-2022

### Пайпы. Доллары, процента, парсинг json для дебага

    // в копоненте есть  float: number = 0.42;
      <p>{{float}}</p> // 0.42;
      <p>{{float | currency}}</p> // выводит $0.42
      <p>{{float | percent}}</p> // выводит 42%
      <p>{{float | percent: '3.1-2'}}</p> // выводит 042.0%
      <p>{{float | percent: '2.0-0'}}</p> // выводит 42%
    
    /*
    удобный pipe для дебага выводит объект как строку,
    но только не [object Object], а как json преобразованный в строку
    */
      <pre>{{obj}}</pre> // выводит [object Object]
      <pre>{{obj | json }}</pre> // выводит <!-- { "a": 1, "b": { "c": 2, "d": { "e": 3, "f": 4 } } } -->

### Пайпы. создание собственного пайпа:

 1.Завести папку pipes в папке app, назвать новый пайп как pipeName.pipes.ts 
 затем в созданный файл добавить такой код( как пример)

    import {Pipe, PipeTransform} from '@angular/core'; // необходимые декораторы и интерфейсы
    
    @Pipe({
        name: 'mult' // название пайпа
    })

    /* интерфейс PipeTransform  удобен чтобы описать метод который принимает значение 
    в пайп и возвращает обработанное значение*/

    export class MultByPipe implements PipeTransform{
            transform(num: any, pow: number = 2): any {
            return num * pow;
        }
    }

### Генерация пайпа через Angular CLi 

В терминале ввести

    ng g p pipes/ex-marks --skip-tests 

Результат в папке pipes, тут уже чуть изменили код чтобы пайп что то делал.

    import { Pipe, PipeTransform } from '@angular/core';
    
    @Pipe({
    name: 'exMarks'
    })
    export class ExMarksPipe implements PipeTransform {
    
    transform(str: string, ...args: any[]): string {
    return `${str.trim()}!!!!`;
    }
    
    }

Использование пайпа в шаблоне компонента:

      <p>{{'Hello World    ' | exMarks}}</p> // выведет Hello World!!!!


### Свой пайп для фильтрации массива по параметру.

Например есть массив posts, есть переменная search(строка поиска), searchField (поле поиска в массиве).

    import {Component, OnInit} from '@angular/core';
    
    export interface Post {
    title: string
    text: string
    }
    
    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss'],
    })
    export class AppComponent {

        search = '';
        searchField = 'title';
        
        posts: Post[] = [
            {title: 'Beer', text: 'The best bread in the world'},
            {title: 'Cookies', text: 'The best cookies in the world'},
            {title: 'js', text: 'The best language in the world'},
            ]
    }

Создаем свой пайп c помощью angular cli:
ng g p pipes/filter --skip-tests

    import { Pipe, PipeTransform } from '@angular/core';
    import {Post} from "../app.component"; // интерфейс поста
    
    @Pipe({
    name: 'filter'
    })
    export class FilterPipe implements PipeTransform {
    
    // принимает массив постов, строку поиска, названия поля для поиска
    transform(posts: Post[], search: string, field: string = 'title'): Post[] {
    
        if(!search.trim()){ //если строка для поиска пустая возвращаем все посты
          return posts;
        }
          return posts.filter(post=>{
            // @ts-ignore - игнор для ts, просит тип для field точнее указать
            return post[field].toLowerCase().includes(search.toLowerCase());
          })
    }
    
    }

Используем самописный пайп

    <div class="container">
      <h1>Angular Components</h1>
    
    <button class="btn" (click)="searchField = 'title'">Title Search</button>
    <button class="btn" (click)="searchField = 'text'">Text Search</button>
    
    <input type="text" [(ngModel)]="search">
      <div  *ngFor="let post of posts | filter:search:searchField" class="card"> // название пайпа:параметр1:параметр2
        <p>{{post.title}}</p>
        <p>{{post.text}}</p>
      </div>
    </div>

### Оптимизация пайпов, Динамические пайпы.

Есть проблема, допустим у нас есть пойп который фильтрует посты см выше самописный пайп filter. Теперь добавили
возможность добавлять посты - нажали на кнопку добавили пост, но наш пайп не отображает этот добавленный пост, даже 
если по фильтрации он должен был отображаться. Дело в том что по умолчанию для повышения производительности пайпы не
перезапускаються при обновлении данных в компоненте. 
Но это поведение можно изменить добавив в декоратор пайпа строку: pure: false. Однако это ухудшит производительность.
Пример всего этого:

html основного компонента где используеться самописный pipe filter:

    <div class="container">
      <h1>Angular Components</h1>
    
    <button class="btn" (click)="searchField = 'title'">Title Search</button>
    <button class="btn" (click)="searchField = 'text'">Text Search</button>
    
    <input type="text" [(ngModel)]="search">
    
    <input type="text" [(ngModel)]="postTitle" placeholder="Post title">
    <input type="text" [(ngModel)]="postText" placeholder="Post text">
    
    <button class="btn" (click)="addPost()">Add Post</button>
    
      <div  *ngFor="let post of posts | filter:search:searchField" class="card">
        <p>{{post.title}}</p>
        <p>{{post.text}}</p>
      </div>
    </div>

ts компонента

    import {Component, OnInit} from '@angular/core';
    
    export interface Post {
        title: string
        text: string
    }
    
    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss'],
    })
    export class AppComponent {
        
        search = '';
        searchField = 'title';
        
        postTitle = '';
        postText = ''
        
        posts: Post[] = [
            {title: 'Beer', text: 'The best bread in the world'},
            {title: 'Cookies', text: 'The best cookies in the world'},
            {title: 'js', text: 'The best language in the world'},
        ]
        
        addPost(): void{
            this.posts.unshift({
                title: this.postTitle,
                text: this.postText
            });
        }
    }

ts самописного пайпа фильтра

    import { Pipe, PipeTransform } from '@angular/core';
    import {Post} from "../app.component";
    
    @Pipe({
        name: 'filter',
        pure: false // это свойсво отвечает за динамическое обновление данных пайпом
    })
    export class FilterPipe implements PipeTransform {
    
    transform(posts: Post[], search: string, field: string = 'title'): Post[] {
    
        if(!search.trim()){
          return posts;
        }
          return posts.filter(post=>{
            // @ts-ignore
            return post[field].toLowerCase().includes(search.toLowerCase());
          })
    }
    
    }

### Пайп Async. Пайп который позволяет работать с асинхронными данными.

В данном примере, будет создан промис который выводит через 4 сек данные, а так же
поток с помощью rxJs и observable, который обновлять данные каждую 1 сек. Пайп async позволяет просто принять
переменную с промисом и выводить данные при resolve, так же и с потоком, просто принимает переменную c observable
и выводим данные.

html в основном компоненте:
    
    <div class="container">
      <h1>Angular Components</h1>
    
    <!--  показ асинхронных даных с помощью pipe async -->
      <p>Wait for...{{p | async }}</p>
      <p>Date: {{date$ | async | date: 'HH:mm:ss YYYY'}}</p>
    
    <!--  Показ асинхронных данных без pipe async-->
    <p>{{date | date: 'HH:mm:ss' }}</p>
    </div>

ts в основном компоненте:

    import {Component, OnInit} from '@angular/core';
    import {Observable} from "rxjs";
    
    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss'],
        })
        export class AppComponent implements OnInit{
        
            p: Promise<string> = new Promise<string>(resolve => {
            setTimeout(()=>{
            resolve('Promise Resolves');
            }, 4000)
            })
            
            // используем Наблюдателя, для дальнейшей обработки данных в подписчике
            date$: Observable<Date> = new Observable(obs => {
                setInterval(() => {
                    obs.next(new Date());
                }, 1000);
            })
            
            date!: Date;
            
            // Показ асинхронных данных без pipe async
            // Реализуем Подписку на события наблюдателя, и запись получаемых данных в переменную когда возникает событие.
            ngOnInit(): void {
                this.date$.subscribe(date=>{
                    this.date = date;
            })
        }
    }

## Сервисы
Сервисы это кастомные или уже существующие классы, которые делят логику обратоки данных. Грубо говоря чтобы не писать
всю логику обработки в ts файле компонента, можно создть отдельный сервис, подключить его к компоненту и использовать
его методы. Так и код чище (декомпозиция) и копирования кода меньше. Важно что сервис это не просто часть общего
кода вынесенного в отдельный класс, это именно отдельный по функционалу и логике блок, который должен быть не зависим
от основого кода и просто исполняет определенную функциональность.

### Создание сервиса

Чтобы создать сервис нужно создать папку services, в ней создать класс сервиса, базово у сервисов нет декораторов.
Сервисы можно так же создавать вручную либоо с помощью angular cli:
Пример ручного создания сервиса.

добавляем папку services в папу app.

Добаляем файл сервиса:

    export class AppCounterService {
    
        counter = 0;
        
        increase(){
            this.counter++;
        }
        
        decrease(){
            this.counter--;
        }
    }

Регистрируем верхнеуровневый сервис в модуле приложения:
        
    // добавляем в массив providers наш сервис
      providers: [ 
        AppCounterService 
    ],

Добавляем в конструктор компонента DI сервиса

    import {Component} from '@angular/core';
    import {AppCounterService} from "./services/app-counter.service";
    
    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss'],
        })
    export class AppComponent{
    
    constructor(public appCounterService: AppCounterService) { // вот сдесь через shortcode ts
    }
    
    }

Используем сервис в html компонента:

    <div class="container">
      <h1>Angular Components</h1>
    
      <div class="card">
        <p>This counter ... {{appCounterService.counter}}</p>
      </div>
    
    <button class="btn" (click)="appCounterService.increase()">+</button>
    <button class="btn" (click)="appCounterService.decrease()">-</button>
    </div>

### Декоратор для сервисов Injectable

Это декоратор, который позволяет в сам сервис инжектировать сущности из других сервисов,
так же в данном декораторе можно задать свойство providedIn: 'root', тогда Angular сам будет регистрировать
в модуле данный сервис.

    import {Injectable} from "@angular/core";
    
    @Injectable({providedIn: 'root'}) // Данный декоратор позволяет в сам сервис инжектировать сущности из других сервисов
    export class AppCounterService {

    }

### Локальные сервисы, создание через angular cli

Так же можно регистрировать сервисы локально, то есть в определенном компоненте, а не целиком во всем модуле приложения.
Для этого нужно добавить в декоратор копонента строчку providers c названием в массиве сервиса который будет работать
в данном компоненте.
Затем инжектировать его в конструкторе данного компонента.

    import {Component} from '@angular/core';
    import {AppCounterService} from "./services/app-counter.service";
    import {LocalCounterService} from "./services/local-counter.service";
    
    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss'],
    providers: [LocalCounterService]
    })
    export class AppComponent{
        
        constructor(
            public appCounterService: AppCounterService,
            public LocalCounterService: LocalCounterService) {
        }
    
    }

создание сервиса c помощью angular cli делаеться так:
    
    ng g s servises/local-counter --skip-test

 ###  Область видимости сервисов

Если сервис добавлен в декораторе компонента в массиве providers: [ServiseName] и инжектирован в конструкторе
компонента, то данный сервис будет иметь локальную область видимости. То есть при изменении данных сервиса
если в нем есть переменная и ее значаение будет изменяться, то это будет происходить
только в области данного компонента.

Другой вариант, это когда нужно чтобы данные были общими 
для компонента родителя и компонента ребенка, тогда добавляем сервис в компонет как di (в конструктор компоннта и конечно
подключаем через импорт), но не добаваляем его в массив providers. Тогда если этот данные сервиса в родителе
и ребенке будут общими. Это очень удобно.

### Инжектирование сервиса внутри другово сервиса

создадим новый компонент ng g s services/log --skip-tests

Получаем:

    import { Injectable } from '@angular/core';

    @Injectable({
        providedIn: 'root'
    })
    export class LogService {
    
    log(text: string) {
        console.log(`Log: ${text}`)
        }
    }

Теперь в сервисе который будет инжектировать LogService так же добавляем его в конструктор как DI

    import {Injectable} from "@angular/core";
    import {LogService} from "./log.service";
    
    @Injectable({providedIn: 'root'}) // Данный декоратор позволяет в сам сервис инжектировать сущности из других сервисов
    export class AppCounterService {

        constructor(private logService: LogService) {
        }

        counter = 0;
        
        increase(){
        this.logService.log('increase counter...');
        this.counter++;
        }
        
        decrease(){
        this.logService.log('decrease counter...');
        this.counter--;
        }
    }

## Валидация

### Валидация форм

В Angular есть два подхода для разработки форм, первый когда формы создаються в шаблоне и второй концепт
Reactive Forms в данном случае будем рассматривать 2 подход, так как рективные формы лучше, более гибкие.

Чтобы создать Reactive Form нужно в модуле их подключить.

    imports: [
    BrowserModule,
    FormsModule,
    ReactiveFormsModule // вот здесь
    ],

Затем в компоненте создаем добавляем сущность формы FormGroup

    import {Component, OnInit} from '@angular/core';
    import {FormGroup} from "@angular/forms";
    
    @Component({
    selector: 'app-root',
    templateUrl: './app.component.html',
    styleUrls: ['./app.component.scss'],
    })
    export class AppComponent implements OnInit{
    
    form!: FormGroup
    
    ngOnInit(): void{
        this.form = new FormGroup({}) // сдесь добавляем сущность FormGroup
    }
    
    submit() {
        console.log('Form submitted :', this.form)
        }
    }

После этого в html компонента, добавляем биндинги для формы и указываем типы кнопок
в данном примере удален код оберток для кнопок, чтобы было нагляднее какие атрибуты добавляються.

      <form class="card" [formGroup]="form" (ngSubmit)="submit()">
          <button class="btn" type="button">Выбрать столицу</button>
          <button class="btn" type="button">Добавить умение</button>
          <button class="btn" type="submit">Отправить</button>
      </form>

Теперь мы соединили отбработку формы html и ts, можно работаь с этим дальше.

### Валдация.Создаание контролов
Контрол это - элемент с помощью которого можно управлять поведением данных в форме, контролы часто используються
для тегов input которые принимаю какие либо данные.

Есть email и pass, необходимо привязать их значения к форме для этого. Опишем объект который передаем в
newForm({})

Логика сдесь такая: форму создаем с помощью класса FormGroup, а отдельный контрол создаеться с помощью 
класса FormControl, который импортируеться из angular/forms.
FormControl принимает в себя несколько аргументов, в начале передаеться начальное значение, например пустая строка
а затем передаеться набор валидаторов. 

import {Component, OnInit} from '@angular/core';
import {FormControl, FormGroup} from "@angular/forms";

    @Component({
        selector: 'app-root',
        templateUrl: './app.component.html',
        styleUrls: ['./app.component.scss'],
    })
    export class AppComponent implements OnInit{
    
    form!: FormGroup
    
    ngOnInit(): void{
        this.form = new FormGroup({ // класс формы
        email: new FormControl(''), // контрол для поля email без валидаторов
        password: new FormControl(null), // контрол для поля password без валидаторов
        })
    }
    
    submit() {
        console.log('Form submitted :', this.form)
        }
    }

Теперь связываем созданые в FormGroup поля FormControl в html в input

    <div class="container">
      <form class="card" [formGroup]="form" (ngSubmit)="submit()">
    
        <h1>Angular Forms</h1>
    
        <input type="text" placeholder="Email" formControlName="email"> 
        <input type="password" placeholder="Пароль" formControlName="password">
        <button class="btn" type="submit">Отправить</button>
      </form>
    </div>

В ts файле компонента, добавим код в методе submit чтобы посмотреть как выводяться данные из контроллов

    submit() {
        console.log('Form :', this.form)
        const formData = {...this.form.value} // добавляем с помощью спреда данные в переменную
        console.log('formData :',formData); // и выводим для наглядности их в консоле
    }

### Валидация данных в контролах

Для добавления валидаторов в конструктор класса FormControl можно вторым параметром добавлять либо один
валидатор либо массив валидаторов. Вот так:

    ngOnInit(): void{
        this.form = new FormGroup({
            email: new FormControl('' , [
                Validators.email, // вызываем как свойство а не функцию
                Validators.required  // вызываем как свойство а не функцию
        ]),
        password: new FormControl('', [
                Validators.required, // вызываем как свойство а не функцию
                Validators.minLength(6) // вызываем метод, потому что нужно передать параметр.
            ])
        })
    }

Validators - дефолтный класс валидаторов, у которого есть статические методы, важно не вызывать функции 
там где валидатор не имеет параметров, потому функция вернет результат несоответсвующий нужному типу (типы 
описаны если навести на класс FormControl (validatorOrOpts?: ValidatorFn | ValidatorFn[] | FormControlOptions | nul)

Добавили валидаторы в ts компонента, теперь сделаем их активными в html
так как данные валидаторы должны валидировать данные на фронте, до отправки формы
то пишев в html следующее, там где кнопка submit

    <button class="btn" type="submit" [disabled]="form.invalid">Отправить</button>

Это значит что кнопка будет неактивна, если форма не прошла валидацию

### Визуализация состояния валидатора

Так как angular при заполнении формы, точне input добавляет к ним определенные классы, которые
характеризуют состояния поля формы. Как пример если класс непроходит валидацию то добавляеться
класс ng-invalid, либо если пользователь начал ввод данных в форму добавиться класс ng-touched.
То опираясь на эту логику, можно добавиьт код в scss:

    input {
        &.ng-invalid.ng-touched{
            border: 2px solid #b06868;
            border-radius: 5px;
        
        }
        &.ng-valid.ng-touched{
            border: 2px solid #66c566;
            border-radius: 5px;
        }
    }

Сдесь если данные классы есть у формы, то будут добавлены определенные css свойства. Таким образом
можно визуализировать свое кастомное отображение валидированности либо заполненности полей формы.

### Отображение ошибок в форме

Чтобы отобразить ошибку пользователю нужно: получить доступ к контролу в html.Вот так.

        <div class="form-control">
          <label>Email</label>
          <input type="text" placeholder="Email" formControlName="email">
    <!--      <pre>{{form.get('email').errors['required'] | json}}</pre>-->
          <div
            *ngIf="form.get('email').invalid && form.get('email').touched"
            class="validation">
            <small
              *ngIf="form.get('email').errors['required']">
              Поле email не может быть пустым
            </small>
            <small
              *ngIf="form.get('email').errors['email']">
              Введите корректный email
            </small>
          </div>
        </div>

Сдесь после input есть div, он будет показывать в случае если форма не прошла валидаци и при этом
форму начали заполнять. В этом div есть два тега small у каждом из них черех *ngif так же стоит условия
в зависимости от того какая именно ошибка валидации была сделана пользователем.
В закомментированном теге pre, код который позволяет понять при его рендере как выглядит объект с ошибками
и что в нем поисходит из него можно взять атрибуты для дальнейшего динамического отображения, вот пример:

    <div class="form-control">
      <label>Пароль</label>
      <input type="password" placeholder="Пароль" formControlName="password">
      <div
        *ngIf="form.get('password').invalid && form.get('password').touched"
        class="validation">
        <pre>{{form.get('password').errors | json}}</pre>
        <small
          *ngIf="form.get('password').errors['required']">
          Поле password не может быть пустым
        </small>
        <small
        *ngIf="form.get('password').errors['minlength']">
          <p>Длинна должна быть не менее {{form.get('password').errors ['minlength'].requiredLength}} символов</p>
          <p>Сейчас количество символов {{form.get('password').errors ['minlength'].actualLength}}</p>
        </small>
      </div>
    </div>

Сдесь показано как отобразить динамически конкретную ошибку валидацию, ошибку по количеству введынх символов
для пароля, начало примера такое же как и для поля email.

### Создание групп в формах
Что это ткое - группы это formGroup, можно внутри группы создавать другие группы, например есть форма
у нее 1 часть поле email, 2 часть поле пароль, 3 часть набор полей (селект и инпут) и вот для этого как раз
применяеться formGroup внутри  formGroup, то есть форма это уже formGroup а внутри него можно еще раз использовать
formGroup.
Пример:

    Код 1 части с input email... 
    Код 2 части с input password... // они описаны выше в главе Отображение ошибок в форме
    
    <div class="card" formGroupName="address">
      <h2>Адрес</h2>

      <div class="form-control">
        <label>Страна</label>

        <select formControlName="country">
          <option value="ru">Россия</option>
          <option value="ua">Украина</option>
          <option value="by">Беларусь</option>
        </select>
      </div>

      <div class="form-control">
        <input type="text" formControlName="city" placeholder="город">
      </div>

      <button class="btn" type="button">Выбрать столицу</button>
    </div>

Добавляем к диву с классом card атрибут formGroupName, тем самым указываем привязываем эту часть
к группе formGroup, далее указываем дял select атрибут formControlName и для input тоже.
теперь чтобы все это увязать с компонентом, пропишем в нем следующее в методе ngOnInit().

    ngOnInit(): void{
        this.form = new FormGroup({
            email: new FormControl('' , [
                Validators.email,
                Validators.required
            ]),
            password: new FormControl('', [
            Validators.required,
            Validators.minLength(6)
            ]),
            address: new FormGroup({ // создаем новую группу
                country: new FormControl('ru'),
                city: new FormControl('',Validators.required)
        })
    })

Добавим событие обработки клика на кнопку выбора столицы в html шаблоне 

    <button class="btn" type="button" (click)="setCapital()>Выбрать столицу</button>

Опишем метод setCapital()

    setCapital(){
    
        // создаем объект соответсвия выбранного поля в FormGroup address->country
        /*благодаря такому объекту динамическая выборка, будет производиться быстро и просто. то есть когда в селекте
        измениться страна (указана названиями свойств) то в соотвествии с этим названием ru,ua,by будет существовать
        столица этой страны.*/
        const cityMap = {
          ru: 'Москва',
          ua: 'Киев',
          by: 'Минск'
        }
    
        const cityKey: string = this.form.get('address').get('country').value // получаем значения поля формы группы address->country
        // @ts-ignore
        const city = cityMap[cityKey]; // выбираем из cityMap значения города по ключю cityKey
    
        this.form.patchValue({address:{ city: city }}); // измениям поле city
    
    }

### Работа с группой

Решаем следующую задачу: есть форма создания резюме, в форме есть поле навыки, есть кнопка "добавить навык"
при нажатии на кнопку должно появляться новое поле input в котором можно добавить навык и при этом это
поле будет валидироваться на пустоту.

ts код: добавляем в ngOnInit() еще один контрол в который кладем объект FormArray,

    ngOnInit(): void{
        this.form = new FormGroup({
            email: new FormControl('' , [
                Validators.email,
                Validators.required
        ]),
            password: new FormControl('', [
            Validators.required,
            Validators.minLength(6)
        ]),
            address: new FormGroup({
                country: new FormControl('ru'),
                city: new FormControl('Москва',Validators.required)
        }),
            skills: new FormArray([]) // вот сдесь
        })
    }

Предполагаем что в объекте FormArray будет изначально лежать пустой массив, но при нажатии на кнопку добавить 
навык в этот массив будут добавляться new FormControl с валидацией на пустоту и они будут подвязываться к input 
html, а массив с контролами будет рендериться через *ngFor, таким образом будут бесконенчо появляться 
необходимое количество полей для новых навыков.

Чтобы добавить FormControl в этот массив, при клике на кнопку "добавить навык" вызываеться метод:

    addSkill(){
    // создаем объект контрола, этот контролл будет добавляться в форму при нажатии кнопки.
    const control = new FormControl('',Validators.required);
    
        // пушим этот контрол в массив
        // (<FormArray>this.form.get('skills')).push(control); // 1 способ типизации
        (this.form.get('skills') as FormArray).push(control); // 2 способ типизации
    }

В Html 

    <div class="card" formArrayName="skills">
      <h2>Ваши навыки</h2>
      <button class="btn" type="button" (click)="addSkill()">Добавить умение</button>
      <div
        class="form-control"
<!--  так не сработает  *ngFor = "let control of form.get('skills').controls; let idx = index"-->
        *ngFor="let control of formDataSkills.controls; let idx = index">
        <label>Навык {{idx + 1}}</label>
        <input type="text" [formControlName]="idx">
      </div>
    </div>

Важное дополнение, обратите внимание что *ngFor работате не с form.get('skills').controls куда мы клали
контролы, там возникает ошибка ts, так как объект который возвращает form.get('skills') имплемментируеться
от абстракного класса в котором нету свойство controls, мы его исскууственно запихиваем в этот объект и ts
ругаеться, типа абстракный класс этого не позволяет.
Поэтому чтобы все работало без ошибок была создана функция геттер в классе компонента

    get formDataSkills(): any{
    return this.form.get('skills');
    }

которая возвращает сам объект FormArray, а дальше уже у него отдельно запрашиваеться объекты controls.

Так же важно обратить внимание, что и для <div class="card" formArrayName="skills"> и
<input type="text" [formControlName]="idx"> мы прописсали formControlName.

### Создание своего валидатора

Валидатор это по сути обычная функция, можно описать ее в классе компонента, а можно создать свой класс
валидатора и подключить его в компоненте, сделаем это 2 способом.

Создаем свой класс валидатора можно в папку app добавить папку validators и в ней
добавить фаил my.validators.ts - на забываем про стаил гайд в названиях

    import {FormControl} from "@angular/forms";
    
    export class MyValidators {
    
    // передаем внутрь объект контрола, возвращаем объект валидатор если есть ошибка
    static restrictedEmails(control: FormControl): {[key:string]:boolean}{
        if(['k@mail.ru','test@mail.ru'].includes(control.value)){
            return {'restrictedEmail': true};
        }
            return null;
        }
    }

Затем добавляем валидатор  в контроллер в ts где валидировался email
    
    ngOnInit(): void{
        this.form = new FormGroup({
            email: new FormControl('' , [
                Validators.email,
                Validators.required,
                MyValidators.restrictedEmails
        ])...code...

И добавлям валидацию в html компонента там где email еще один тег small

        <small
        *ngIf="form.get('email').errors['restrictedEmail']">
          Email {{form.get('email').value}} запрещен!
        </small>

### Асинхронные валидаторы

Асинхронный валидтаром может использоваться для ситуаций, когда данные в форме сравниваються с данными
приходящими по запросу, и соответсвенно валидация будет ожидать прихода данных.

Создадим свой асинхронный валидатор:

В app/my.validators.ts (создавали в главе выше) в классе MyValidators
добавим еще метод

     static uniqEmail(control:  AbstractControl):  Promise<ValidationErrors> | Observable<ValidationErrors> {
       return new Promise(resolve=>{
         setTimeout(()=>{
           if(control.value === 'async@mail.ru'){
             resolve({uniqEmail: true})
           }else{
             resolve(null);
           }
         },2000)
       })
     }

Стоит обратить внимание, что в параметры данного метода передаються определенные типы, которые указаны для
класса FormGroup, см 3 параметр AsyncValidatorFn
    
    // описание типов параметров используемых в конструкторе форм
    FormGroup.constructor( 
        controls: {[p: string]: AbstractControl},
        validatorOrOpts?: ValidatorFn | ValidatorFn[] | AbstractControlOptions | null,
        asyncValidator?: AsyncValidatorFn | AsyncValidatorFn[] | null)
    
    //описание интефейса 3 параметра конструктора и вот откуда типы в асинхронном валидаторе.
    export declare interface AsyncValidatorFn {
    (control: AbstractControl): Promise<ValidationErrors | null> | Observable<ValidationErrors | null>;
    }
 
Добавляем валидатора в классе компонента в методе ngOnInit

     ngOnInit(): void{
       this.form = new FormGroup({
         email: new FormControl('' , [
           Validators.email,
           Validators.required,
           MyValidators.restrictedEmails
         ], [MyValidators.uniqEmail]), // сдесь 3 параметром
         password: new FormControl('', [
           Validators.required,
           Validators.minLength(6)
         ]),
         address: new FormGroup({
           country: new FormControl('ru'),
           city: new FormControl('Москва',Validators.required)
         }),
         skills: new FormArray([])
       })
     }

А так же добавляем в его в html формы 

    <div class="form-control">
      <label>Email</label>
      <input type="text" placeholder="Email" formControlName="email">
    <!--      <pre>{{form.get('email').errors['required'] | json}}</pre>-->
      <div
        *ngIf="form.get('email').invalid && form.get('email').touched"
        class="validation">
        <small
          *ngIf="form.get('email').errors['required']">
          Поле email не может быть пустым
        </small>
        <small
          *ngIf="form.get('email').errors['email']">
          Введите корректный email
        </small>
        <small
        *ngIf="form.get('email').errors['restrictedEmail']">
          Email {{form.get('email').value}} запрещен!
        </small>
        <small
          *ngIf="form.get('email').errors['uniqEmail']">
          Email {{form.get('email').value}} занят!
        </small>
      </div>
    </div>

Так же можно указать чтобы кнопка submit была неактивна, пока асинхронная валидация не прошла
form.pending

    <button class="btn" type="submit" [disabled]="form.invalid || form.pending">Отправить</button>

### Очистка формы
Обычно используеться после отправки формы, очищает все поля формы. Чтобы добавить подобный функционал
досточно в метод который отвечает за отправку формы добавить строчку this.form.reset();

    submit() {
      if(this.form.valid){
        console.log('Form is valid');
        console.log('Form :', this.form)
        const formData = {...this.form.value}
        console.log('formData :',formData);
      }
      this.form.reset(); // очистка формы
    }



## Создание своей ngModel

О чем реч, иногда может понадобиться создать ngModel но только не для inputов форм, а для компонента.
Так чтобы данные из компонента родителя могли передаваться в компонент ребенка, и при этом был двухстороний
биндинг, то есть если поменять эти данные в компоненте родителя, то они менялись и в компоненте ребенка. А 
так же если поменять эти данные в компоненте ребенка они менялись и в компоненте родителя. И все это работало
аналогично двухстороннему биндингу. 

Примечание: все это так же можно и делаеться с помощью деораторов @Input и @Output описанных в начальных
главах, там где биндинги описываються, но это другой способ, сочитать сразу и то и другое, причем чтобы
оно работало автоматически.

Пример: в дочерний компоненте есть две кнопки, on и off, они переключают состояние 
переменнной в дочернем компоненте на 'on' или 'off'. В родительском компоненте тоже есть переменная состояние 
которой может быть 'on' или 'off' и нужно организовать двухстороннюю связь, чтобы нажатие кнопок а дочернем
компоненте измняло состояние родительского и наоборот.

Для этого создаем дочерний компонент, например c названием switch

html компонента switch:
    <div>
      <button [class.active]="state === 'on'" (click)="setState('on')">On</button>
      <button [class.active]="state ==='off'" (click)="setState('off')">Off</button>
    </div>

css компонента switch:

    div{
      display: inline-block;
    }
    button{
      padding: .5rem 1rem;
      font-size: 1rem;
      color: #fff;
      background: red;
      border: none;
      outline: none;
    
      &.active{
        background: green;
      }
    }

имеем две кнопки, при нажатии на каждую меняеться состояние переменной state в компоненте switch

ts компонента switch:

    import {Component, forwardRef, Provider} from '@angular/core';
    import {ControlValueAccessor, NG_VALUE_ACCESSOR} from "@angular/forms";
    
    //переменная accessor
    const VALUE_ACCESSOR: Provider = {
      provide: NG_VALUE_ACCESSOR,
      useExisting: forwardRef(() => {return SwitchComponent}), // колбек с названием компонента
      multi: true
    }
    @Component({
      selector: 'app-switch',
      templateUrl: './switch.component.html',
      styleUrls: ['./switch.component.scss'],
      providers: [VALUE_ACCESSOR] // подключаем аксессор
    })

    // акссесор есть, нужно имплементировать интерфейс ControlValueAccessor и его методы
    export class SwitchComponent implements ControlValueAccessor{
    
      state = 'off';
    
    // приватная функция которая принимаеет значение
      private onChange = (value: any)=>{} 
    
      setState(state: string){
        this.state = state;
    
        this.onChange(this.state); // при вызове метода inChange anular будет самостоятельно "аутпутить"
        // изменения наверх, в родительский компонент.
      }
    
        // принимает в себя функцию которая будет следить за изменениями,
        // регистрируем в ней метод onChange
      registerOnChange(fn: any): void {
        this.onChange = fn;
      }
        // принимает в себя функцию которая будет отрабатывать при касании экрана на мобильно телефоне
      registerOnTouched(fn: any): void {
      }
        
        // этот метод позволяет заблокировать модель
      setDisabledState(isDisabled: boolean): void {
      }
        // позволяет принять значение от ngModel и записать его в локальную переменную
      writeValue(state: string): void {
        this.state = state;
      }
    
    }

Код уже готовый, основные моменты:
Создаем переменную VALUE_ACCESSOR с типом Provider, заполняем как в примере.
Добавляем ее в декоратор класса через свойство providers.
Имплементируем класс от интерфейса ControlValueAccessor.
Добавляем функции прописанные в этом интерфейсе.
создаем метод переменную private onChange = (value: any)=>{} в ней колбек должен быть.
Регистрирруем эту функцию в методе registerOnChange() как в коде.
Добавляем в метод writeValue this.state = state;
Название переменных могут меняться, но принцип такой.

Как это все работает:
У нас есть метод setState который мы локально вызываем, в нем можно писать разную логику.
Когда эта логика сработате, нужно вызвать метод onChange(this.state) чтобы сообщить что модель изменилась
и в него передать состояние.
Чтобы принять состояние извне в компонент используеться метод writeValue в данном случае
в нем прописываеться this.state = state, то есть он служит чтобы переданное в атрибут [(ngModel)]
значение appState отобразилось сразу при загрузке дочернего элемента.

код в родительском html.

    <app-switch [(ngModel)]="appState"></app-switch>

Так же при изменении состояния в компоненте ребенке в созданном ngModel, можно добавить атрибут
(ngModelChange)="someHandle()" , это позволит обрботать событие измеения состояния,
но уже в компоненте родителя, иначе говоря отслеживать в родителе изменение ngModel 

html родителя

    // используем дочерний элемент с созданным ngModel
    <app-switch
    [(ngModel)]="appState"
    (ngModelChange)="handleChange()"
    >
      </app-switch>

ts родителя

        //code....
        handleChange(){
            console.log(this.appState)
        }
        //code...

 ## Работа с запросами на сервер HttpClient

### HttpClient. Получение данных и вывод их в шаблон
Для работы с запросами в Angular есть свой специльный модуль HttpClientModule.
Подключим его в главном модуле app.modules.ts

    import {HttpClient} from "@angular/common/http";
    //code...
    imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule
    ],

Теперь в основном компоненте пропишем инжекцию HttpClient в конструкторе,HttpClient он работает на RxJs. 
когда мы выполняем метод this.http.get() this.http.post() или другие методы,angular оборачивает все это в
RxJs стрим, когда стрим создан RxJs регистрирует данный стрим, но не выполняет его. Это сделано для 
оптимизации. Чтобы вполнить стрим, нужно добавить слушателя, то есть подписаться на него с 
помощью метода subscribe. Сделаем запрос на тестовое Api, так же добавим интерфейс который будет
характеризовать типы объектов получаемых в ответе на запрос, 



    import {Component, OnInit} from '@angular/core';
    import {HttpClient} from "@angular/common/http";
    
    // интерфейс объекта ответа
    export interface Todo {
      completed: boolean
      title: string
      id?: number
    }

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
    })
    export class AppComponent implements OnInit{
    
      todos: Todo[] = [];
    
      constructor(private http: HttpClient) {}
    
        // чтобы типы в this.todos = responce сопадали нужно указать какого именно типа будет приходить
        // ответ
      ngOnInit(){
        this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?_limit=2')
          .subscribe(responce => {
            console.log('Response',responce);
            this.todos = responce
          })
      }
    }

Теперь выведем эти данные в шаблон, идея сдесь это список дел todo.

    <div class="container">
      <h1>Angular HttpClient</h1>
    
      <div class="card">
        <div class="form-control">
          <input type="text" placeholder="Название">
        </div>
        <button class="btn">Добавить</button>
        <button class="btn">Загрузить</button>
      </div>
    
      <hr>
    
      <div class="card" *ngFor="let todo of todos">
        <p>
          <span [class.completed]="todo.completed">{{todo.title | titlecase}}</span>
          <span>
            <button class="btn btn-danger">Удалить</button>
            <button class="btn" [disabled]="todo.completed">Завершить</button>
          </span>
        </p>
      </div>
    
    </div>

### HttpClient. Получение данных с POST запросом

В html компонента добавляем ngModel на input и обработчик клика для добавления todo на кнопку

      <div class="card">
        <div class="form-control">
          <input type="text" placeholder="Название" [(ngModel)]="todoTitle">
        </div>
        <button class="btn" (click)="addTodo()">Добавить</button>
        <button class="btn">Загрузить</button>
      </div>

в ts компонента добавляем метод addTodo() в нем делаем проверку на заполненность,
    создаем объект нового toDo и отправляем запрос pos положив в него объект todo,
    вернется объект уже с id, пушем его в массив тудусов и стераем title в input в ngModel

    addTodo(){
      if(!this.todoTitle.trim()){
        return
      }
      const newTodo: Todo = {
        title: this.todoTitle,
        completed: false
      }
    
      this.http.post<Todo>('https://jsonplaceholder.typicode.com/todos', newTodo)
        .subscribe(todo=>{
          this.todos.push(todo);
          this.todoTitle = '';
        })
    }

### HttpClient. RxJs операторы и индикатор загрузки

Добавим индикатор загрузки при получении списка todos

в файле ts есть метод fetchTodos(), который возвращает все todos по запрос в Api
добавляем в него флаг loading, а к этом флагу в html подцепляем *ngif c html лоадера. Так же сдесь использован
pipe(delay(1500)) pipe это метод RxJs cтримов, в нем оператор задержки из библиотеки RxJs operators

    fetchTodos(){
      this.loading = true;
      this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?_limit=2')
        .pipe(delay(1500))
        .subscribe(todos => {
          this.todos = todos
          this.loading = false;
        })
    }

html

      <div *ngIf="!loading; else loadingBlock">
        <div class="card" *ngFor="let todo of todos">
          <p>
            <span [class.completed]="todo.completed">{{todo.title | titlecase}}</span>
            <span>
            <button class="btn btn-danger">Удалить</button>
            <button class="btn" [disabled]="todo.completed">Завершить</button>
          </span>
          </p>
        </div>
      </div>
    
    <ng-template #loadingBlock>
    <p>Loading...</p>
    </ng-template>

### HttpClient. Удаление элементов методом DELETE

нужно удалить todoшки, к кнопке Удалить добавляем (click)="removeTodo(todo.id), а 
так же если все тодушки удалены, то показываем сообщеине с помощью *ngIf 
html:

    <div *ngIf="todos.length; else noTodos">
      <div class="card" *ngFor="let todo of todos">
        <p>
          <span [class.completed]="todo.completed">{{todo.title | titlecase}}</span>
          <span>
        <button class="btn btn-danger" (click)="removeTodo(todo.id)">Удалить</button>
        <button class="btn" [disabled]="todo.completed">Завершить</button>
      </span>
        </p>
      </div>
    </div>

    <ng-template #noTodos>
     <p>No Todos</p>
    </ng-template>

не забываем что для удаление нужно педелать todo.id в метод запроса DELETE и добавить тип возвращаемого дженерика
для this.http.delete
ts 

    removeTodo(todoId: number){
        this.http.delete<void>(`https://jsonplaceholder.typicode.com/todos/${todoId}`)
        .subscribe(resp=>{
            this.todos = this.todos.filter(t => t.id !== todoId )
        })
    }

### HttpClient. Перенос логики todoшника в сервис

В чем суть, по предыдущим главам был создан тодушник, который общаеться со сторонним API, методами
GET получить массив объектов тудушек, POST - добавить свою тудушку, DELETE - удалить тудушку.
Вся логика обращения в API с помощью HttpClient была прописана в самом ts компонента, что не хорошо.
Будем делить логику, а именно создадим сервис который будет работать с Апи и иметь отдельно методы обращения
в это апи.

создаем сервис ng g s todos --skip-tests и переносим часть кода из функций комонента

    import { Injectable } from '@angular/core';
    import {HttpClient} from "@angular/common/http";
    import {delay, Observable} from "rxjs";
    
    export interface Todo {
      completed: boolean
      title: string
      id?: number
    }
    
    @Injectable({
      providedIn: 'root'
    })
    export class TodosService {
    
      constructor(private http: HttpClient) {}
    
      addTodo(todo: Todo): Observable<Todo> {
        return this.http.post<Todo>('https://jsonplaceholder.typicode.com/todos', todo)
      }
    
      fetchTodos(): Observable<Todo[]>{
        return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?_limit=2')
          .pipe(delay(500));
      }
    
      removeTodo(todoId: number): Observable<void>{
        return this.http.delete<void>(`https://jsonplaceholder.typicode.com/todos/${todoId}`)
      }
    
    }

Импортируем @Injectable, указываем providedIn: 'root' чтобы ангуляр зарегистрировал сервис в главном
модуле сам. Добавляем методы addTodo(),fetchTodos(),removeTodo(), параметры такие же как были в компоненте
у этих методов.Так же переносим интерфейс interface Todo из компонента. 
Каждый метод возвращает тип Observable. В компоненте подключаем наш сервис.

    import {Component, OnInit} from '@angular/core';
    import {Todo, TodosService} from "./todos.service";
    
    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
    })
    export class AppComponent implements OnInit{
    
      loading = false;
      todos: Todo[] = [];
      todoTitle = '';
    
      constructor( private  TodosService: TodosService) {}
    
      ngOnInit(){
        this.fetchTodos();
      }
    
      addTodo(){
        if(!this.todoTitle.trim()){
          return
        }
    
        this.TodosService.addTodo({
          title: this.todoTitle,
          completed: false
        }).subscribe(todo=>{
          this.todos.push(todo);
          this.todoTitle = '';
        })
      }
    
      fetchTodos(){
        this.loading = true;
        this.TodosService.fetchTodos().subscribe(todos => {
          this.todos = todos
          this.loading = false;
        })
      }
    
      removeTodo(todoId: number){
        this.TodosService.removeTodo(todoId).subscribe(resp=>{
          this.todos = this.todos.filter(t => t.id !== todoId )
        })
      }
    }

Используем созданный сервис. Теперь сама логика запросов на Апи находиться в сервисе,
а логика обработки полученных данных осталась в компоненте.

### HttpClient. Модификация элементов с PUT

Добавим метод по модификации элементов тудушек, в данном случае нужно добавить функционал в котором
при нажатии кнопки завершить поле completed объекта тудушки будет переводиться в true, причем не только
в нашем массиве тудушек на клиенте, но и через апи, например если состояние тудушек храниться в бд. 
Чтобы отправить запрос на изменения состояния тудушки используем PUT запрос, а дальше он уже на беке
будет обращаться в бд и менять ее состояние. Бек сейчас не интересен, интересно как через ангуляр 
отпраить PUT запрос на изменение.

в html добавляем действие по клику на кнопку

    <button class="btn" [disabled]="todo.completed" (click)="completeTodo(todo.id)">Завершить</button>

в todos.service.ts добавляем метод completeTodo() принимающий idшник тудушки

    completeTodo(todoId: number): Observable<Todo>{
        return this.http.put<Todo>(`https://jsonplaceholder.typicode.com/todos/${todoId}`,{
            completed: true
        });
    }
запрос возвращает нам объект тудушки типа такого { id: 3, completed: true}, в компонента
используя этот метод сервиса, так же меняем статус тудушки на completed: true в массиве
тудушек на клиенте

    completeTodo(id:number) {
        this.TodosService.completeTodo(id).subscribe(todo => {
            this.todos.find ( t=>t.id === todo.id).completed = true;
        })
    }

Готово, теперь тудушку можно завершить кликнув на кнопку.
В html, так же можно использовать класс который будет добавлять при заверешнеии тудушки
например как сдесь в теге span в котором выводился текст тудушки, по факту ее заголовок, если
todo.completed === true, тогда применяеться класс completed.

      <div *ngIf="!loading; else loadingBlock">
        <div *ngIf="todos.length; else noTodos">
          <div class="card" *ngFor="let todo of todos">
            <p>
              <span [class.completed]="todo.completed">{{todo.title | titlecase}}</span>
              <span>
            <button class="btn btn-danger" (click)="removeTodo(todo.id)">Удалить</button>
            <button class="btn" [disabled]="todo.completed" (click)="completeTodo(todo.id)">Завершить</button>
          </span>
            </p>
          </div>
        </div>
    
        <ng-template #noTodos>
         <p>No Todos</p>
        </ng-template>
    
      </div>

### HttpClient. Обработка ошибок с httpClient
В целом есть множество возможностей обрабатываеть ошибки, как со стороны httpClient в методе subscribe(),
так и еще раньше при работе с запросами с помощью RxJs в методе pipe.

Чтобы обработать ошибку с помощью httpClient используем метод subscribe() который помимо ответа на запрос
может принимать еще два параметра кол-бэк функции. 1  колл-бэк принимает в себя данные при успешно отработанном
запросе, 2 колл-бэк принимает в себя ошибку, если произошла ошибка,
3 колл-бэк без параметров, вызываеться когда стрим завершил свое выполнение.
Добавим в subscribe обработку ошибки, при получении тудушек после запроса в Апи, а ттак же с помощью
*ngIf отобразим ее в html

ts 

    error = '';
    //code...
    fetchTodos(){
        this.loading = true;
        this.TodosService.fetchTodos().subscribe(todos => {
            this.todos = todos
            this.loading = false;
        }, error => {
            console.log(error.message);
            this.error = error.message;
        })
    }

html

    <p *ngIf="error" else todosBlock class="error">{{error}}</p>-->
    <ng-template #todosBlock>-->
      <div *ngIf="!loading; else loadingBlock">-->
        <div *ngIf="todos.length; else noTodos">-->
          <div class="card" *ngFor="let todo of todos">-->
            <p>-->
              <span [class.completed]="todo.completed">{{todo.title | titlecase}}</span>-->
              <span>-->
          <button class="btn btn-danger" (click)="removeTodo(todo.id)">Удалить</button>-->
          <button class="btn" [disabled]="todo.completed" (click)="completeTodo(todo.id)">Завершить</button>-->
        </span>-->
            </p>-->
          </div>-->
        </div>-->
        <ng-template #noTodos>-->
          <p>No Todos</p>-->
        </ng-template>-->
      </div>-->
    </ng-template>

Помимо этого можно использовать встроенные инструменты в RxJs для обработки ошибок, для этого
в методе pipe добавим еще оператор, в целом в метод pipe() можно добавлять бесконечное количество
операторов RxJs. Чтоб отловить ошибку будем использовать оператор catchError(), который принимает в себя
call-back который принимает как параметр объект ошибки. Так же нужно будет вернуть тип Observable, для этого
в данном случае пропишем return throwError(error), это так же метод из RxJs, который возвращает тип Observable.
Теперь в сервисе todos.service.ts добавим код в fetchTodos().

    fetchTodos(): Observable<Todo[] >{
      return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todoss?_limit=2')
        .pipe(
          delay(500),
          catchError(error => {
            console.log('Error: ', error.message)
            return throwError(error);
          }));
    }

Теперь мы уже в стриме получаем ошибку и можем с ней что то сделать обратать прям сдесь, либо добавить
обработчик.

### HttpClient. Добавление заголовков в httpClient

Иногда нужно добавлять свои headers к запросам, чтобы это сделать нужно добавить 3 параметр
в post, для get это 2 параметр
Добавляем в post 3 параметр объект с заголовками вот так

    import { Injectable } from '@angular/core';
    import {HttpClient, HttpHeaders} from "@angular/common/http";
    import {catchError, delay, Observable, throwError} from "rxjs";
    
    export interface Todo {
      completed: boolean
      title: string
      id?: number
    }
    
    @Injectable({
      providedIn: 'root'
    })
    export class TodosService {
    
      constructor(private http: HttpClient) {}
    
      addTodo(todo: Todo): Observable<Todo> {
        return this.http.post<Todo>(
          'https://jsonplaceholder.typicode.com/todos', // адрес запроса
          todo, // отправленная информация в теле запроса
          {headers: new HttpHeaders({ // добавленные заголовки
              'MyCustomHeaders': Math.random().toString(),
              'Dsdssdf':'234234'
            })
          })
      }

### HttpClient. Добавление параметров в запрос:

Параметры в запрос можно добавлять не только в строку запроса, но так же программно в объект
параметра options для метода запроса. Передавать можно один или несколько параметров.
Пример с передачей 1 параметра в запросе

    fetchTodos(): Observable<Todo[] >{
        // такая запись даст сроку запроса https://jsonplaceholder.typicode.com/todos?_limit=3
      return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos', {
        params: new HttpParams().set('_limit','3') // добавили параметр запроса
      })
        .pipe(
          delay(500),
          catchError(error => {
            console.log('Error: ', error.message)
            return throwError(error);
          }));
    }

Пример с передачей нескольких параметров

    fetchTodos(): Observable<Todo[] >{
      let params = new HttpParams(); // добавляем параметр через let, так как объект будет меняться
      params = params.append('_limit', '4'); // такая конструкция возвращает новый объект params
      params = params.append('custom', 'anything');
    
      return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?', {
        params: params
      })
        .pipe(
          delay(500),
          catchError(error => {
            console.log('Error: ', error.message)
            return throwError(error);
          }));
    }

### HttpClient. Параметр Observe и 
Здесь речь идет о параметре который добавляеться в объект options при отправлении запроса
С помощью параметра observe можно указывать какой тип данных мы хотим получить в ответе:
events, response, body, по умолчанию идет тип body

    return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?', {
      params: params,
      observe: 'body' // по умолчанию
    })

body это уже подготовленный, обработанные данные ответа, без статусов, чисто само тело ответа с данными.
Получим тип response 

fetchTodos(): Observable<Todo[] >{
let params = new HttpParams(); // добавляем параметр через let, так как объект будет меняться
params = params.append('_limit', '4');
params = params.append('custom', 'anything');

    return this.http.get<Todo[]>('https://jsonplaceholder.typicode.com/todos?', {
      params: params,
      observe: 'response' // изменили тип получаемого ответа, теперь будем получать весь ответ, а не только тело.
    })
      .pipe(
        map(response=>{ // нужно замапить response и вернуть body, чтобы ts не ругался
          console.log(response) 
          return response.body // внутри метода map возвращаем body
        }),
        delay(500),
        catchError(error => {
          console.log('Error: ', error.message)
          return throwError(error);
        }));
    }

Что получаем с помощью observe: 'response', получаем более детальный ответ такого вида:

    HttpResponse {headers: HttpHeaders, status: 200, statusText: 'OK', url: 'https://jsonplaceholder.typicode.com/todos?_limit=4&custom=anything', ok: true, …}
        body: Array(4)
            0: {userId: 1, id: 1, title: 'delectus aut autem', completed: false}
            1: {userId: 1, id: 2, title: 'quis ut nam facilis et officia qui', completed: false}
            2: {userId: 1, id: 3, title: 'fugiat veniam minus', completed: false}
            3: {userId: 1, id: 4, title: 'et porro tempora', completed: true}
            length: 4
            [[Prototype]]: Array(0)
        headers: HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
        ok: true
        status: 200
        statusText: "OK"
        type: 4
        url: "https://jsonplaceholder.typicode.com/todos?_limit=4&custom=anything"
        [[Prototype]]: HttpResponseBase

Здесь и наше поле body и другие поля и теперь можно при получении ответа, делать что то дополнительно.
В данном ответе присутствует поле 'type:4' это поле говорит на каком этапе находиться состояние запроса.
Как и в ajax есть этапы формирования запроса, так и сдесь есть нечто подобное. 
Для того чтобы получить доступ до всех состояний/событий запроса можно использовать поле observe: 'events'.

    removeTodo(todoId: number): Observable<any>{ // сдесь был Observable<void>  но это если мв ничего не возвращаем
      return this.http.delete<void>(`https://jsonplaceholder.typicode.com/todos/${todoId}`,{
        observe: 'events'
      }).pipe(
        tap(event=>{
          console.log(event)
        })
      )
    }

tap - оператор который так же импорируеться из RxJs он нужен для перехвата данных,
данный console.log(event) выведет 

    {type: 0} // сначало идет этап отправки данных 0

    todos.service.ts:55
    HttpResponse {headers: HttpHeaders, status: 200, statusText: 'OK', url: 'https://jsonplaceholder.typicode.com/todos/4', ok: true, …}
    body: {}
    headers: HttpHeaders {normalizedNames: Map(0), lazyUpdate: null, lazyInit: ƒ}
    ok: true
    status: 200
    statusText: "OK"
    type: 4 // затем запрос выполняеться этап 4
    url: "https://jsonplaceholder.typicode.com/todos/4"
    [[Prototype]]: HttpResponseBase

каждый type: 0,1,2,3,4,5 отвечает за стадию формирования запроса..типа запрос подготовлен, запрос 
отправлен, запрос ожидаеться и тд.
Теперь как это использовать

     removeTodo(todoId: number): Observable<any>{
      return this.http.delete<void>(`https://jsonplaceholder.typicode.com/todos/${todoId}`,{
        observe: 'events'
      }).pipe(
        tap(event=>{
          if(event.type === HttpEventType.Sent){ // если отправка прошла, то...
            console.log('Sent',event);
          }
          if(event.type === HttpEventType.Response){ // если ответ пришел то...
            console.log('Response',event);
          }
        })
      )
    }
HttpEventType импортруеться из 

    import {HttpClient, HttpEventType, HttpHeaders, HttpParams} from "@angular/common/http";

и содержит набор енумов, которые соответсвуют состоянию запросов по названию.

Еще один параметркоторый присутствует в options запроса это 'responseType'. Этот параметр отвечает
за то в каком виде/типе будет возвращен запрос, обычно это происходит в json типе, который затем
ангуляр парсит в объект, но это можно поменять. Например вернуть просто строку json.

    completeTodo(todoId: number): Observable<Todo>{
      return this.http.put<Todo>(
        `https://jsonplaceholder.typicode.com/todos/${todoId}`,
        {completed: true},
        {responseType: 'json'} // вот так это выглядит в явном виде но можно поменять на 'text'
      );
    }
 
Так же ест возможность возвращать типы 
arraybuffer,
blob,
text - angular сразу передаст нам строку с данными, без парсинга
помимо json.

### HttpClient. Перехват запросов с интерсептором.
Интерсептор это классы которые позволяют перехватывать http запрос и что то с этим делать.
Интерсептор так же можно считать сервисом, чтобы его создать добавим фаил с именем
auth.interceptor.ts, создадим в нем класс и имплементируем его от интерфейса HTTPInterceptor который
импортирует из бибилиотеки @angular/common/http, теперь выполним условия интерфейса и создадим метод
intercept c требуемыми параметрами:

    import {HttpEvent, HttpHandler, HttpInterceptor, HttpRequest} from "@angular/common/http";
    import {Observable} from "rxjs";
    
    export class AuthInterceptor implements  HttpInterceptor{
      intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log('Intercept request', req); // посмотрим что мы перехватываем запрос
        return next.handle(req); 
        /*
        Если посмотреть на сигнатуру next то увидем что у этого метода есть метод
        handle он принимает объект запроса и как раз возвращает требуемый Observable<HttpEvent<any>>
        */
      }
    }

Далее нужно зарегистрировать в модуле этот interceptor
В модуле нужно добавить его в массив providers, но это нужно сделать через создании константы переменной
INTERCEPTOR_PROVIDER, ее тип Provider и она содержит объект с полями
* provide равным HTTP_INTERCEPTORS это специальная константа из библиотеки angular/common/http
* useClass: AuthInterceptor - передаем название того интерсептора который будем использовать
* multi: true - если будет несколько интерсепторов в приложении, чтобы они не перезатирались, 
а добавлялись поочередно, если указать false то будет доступен только 1 интерсептор в приложении.

Далее добавляем интерсептор в массив providers.



    import {NgModule, Provider} from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';
    import { FormsModule, ReactiveFormsModule} from "@angular/forms";
    import {HTTP_INTERCEPTORS, HttpClientModule} from "@angular/common/http";
    import { AppComponent } from './app.component';
    import {AuthInterceptor} from "./auth.interceptor";
    
    const INTERCEPTOR_PROVIDER: Provider = {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    }
    
    @NgModule({
      declarations: [
        AppComponent,
      ],
      imports: [
        BrowserModule,
        FormsModule,
        HttpClientModule
      ],
      providers: [
        INTERCEPTOR_PROVIDER
      ],
      bootstrap: [AppComponent]
    })
    export class AppModule { }

Если будет несколько интерсепторов, которые нужно будет зарегистрировать, то проще добавлять их через
функцию, которая будет принимать параметр в виде класса интерсептора useClass, и возвращать целиком
объект интерсептора.

### HttpClient. Модификация запросов и обработка ответов.

Теперь модифицируем запрос, который был получен с помощью интерсептора, чтобы что то сделать, например
добавить заголовок. Для этого нам нужно склонировать перехваченный interceptorom запрос, в него передать
объект update который содержит элементы который нужно добавить или изменить (добавим туда хедер).
Так же так как данная конструкция являеться стримом, то можно вызвать любые методы доступные для стримов

    import {HttpEvent, HttpEventType, HttpHandler, HttpInterceptor, HttpRequest} from "@angular/common/http";
    import {Observable, tap} from "rxjs";
    
    export class AuthInterceptor implements HttpInterceptor{
      intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log('Intercept request', req);
        const cloned = req.clone({
          headers: req.headers.append('Auth', 'SOME RANDOM TOKEN') // добавили хедер
        })
    
        return next.handle(cloned).pipe( // добавляем методы и операторы стримов 
          tap(event=>{
            if(event.type === HttpEventType.Response){
              console.log('Interceptor response', event);
            }
          })
        )
      }
    }

## Роутинг

В этой главе речь будет идти о роутинге, посмотрим как с ним работать.

### Роутинг. Регистрация роутов.
 Схема работы роутов примерно такая, у нас есть отдельные компоненты, которые имеют свой html
 отображения своих страниц. Эти компоненты зарегистрированы в главном модуле. Теперь в этот же 
 главный модуль нужно подключить модуль роутинга, который нужно создать, в этом модуле роутинга
 будут прописаны роуты и компоненты которые будут вызываться когда этот роут будет в адресной строке.
 Приступим:
 
Компоненты для отображения сраниц уже есть, создадим модуль роутинга. Для этог в папке app добавим
фаил app-routing.module.ts в нем пропишем код:

    import {NgModule} from "@angular/core"; // импорт декоратора модуля
    import {RouterModule, Routes} from "@angular/router"; // импорт роутера
    import {HomeComponent} from "./home/home.component"; // компонент 
    import {AboutComponent} from "./about/about.component"; // компонент 
    import {PostsComponent} from "./posts/posts.component"; // компонент 
    
    // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
    // http://localhost:4200/about -> AboutComponent адрес страницы about
    // http://localhost:4200/posts -> PostsComponent aдрес старницы posts
    
    // регистрируем роуты для компонентов в массив {путь,название компонента}
    const routes: Routes = [
      {path: '', component: HomeComponent},
      {path: 'about', component: AboutComponent},
      {path: 'posts', component: PostsComponent}
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)], // какие данные будут использоваться этим модулем
    // в данном случае передаем массив роутев routes в метод forRoot()
      exports: [RouterModule] // какие данные разрешаем использовать из этого модуля другим модулям.
    })
    export class AppRoutingModule {
    
    }

Теперь роутинговый модуль нужно зарегистрировать в главном модуле

    import {BrowserModule} from '@angular/platform-browser'
    import {NgModule} from '@angular/core'

    import {AppComponent} from './app.component'
    import {FormsModule} from '@angular/forms'
    import {AboutComponent} from './about/about.component'
    import {HomeComponent} from './home/home.component'
    import {PostsComponent} from './posts/posts.component'
    import {PostComponent} from './post/post.component'
    import {AboutExtraComponent} from './about-extra/about-extra.component'
    import {AppRoutingModule} from "./ app-routing.module";
    
    @NgModule({
      declarations: [
        AppComponent,
        AboutComponent,
        HomeComponent,
        PostsComponent,
        PostComponent,
        AboutExtraComponent,
      ],
      imports: [
        BrowserModule,
        FormsModule,
        AppRoutingModule // регистрируем роутинговый модуль
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule {
    }

И последний шаг, нужно изменить html в файле вида базового компонента app.component.html сдесь пропишем
подключаемый компонент <router-outlet></router-outlet> вместо статичного названия одного из компонентов.

    <nav class="navbar">
      <h1>
        <a href="#">Angular Routing</a>
      </h1>
      <ul>
        <li class="active">
          <a href="#">Home</a>
        </li>
        <li><a href="#">Posts</a></li>
        <li><a href="#">About</a></li>
        <li><button class="btn">Login</button></li>
        <li><button class="btn">Logout</button></li>
      </ul>
    </nav>
    
    <div class="container">
      <div class="card">
        <router-outlet></router-outlet> // вместо <app-home></app-home>
      </div>
    </div>

### Роутинг. Динамическая смена страниц
Обычно при переходе на страницу по ссылке типа href="/somePage", страница заного загружаеться,
но это не правильное поведение для single page application, необходимо чтобы перезагрузка была
динамической, то есть перезагружались только отображаемые данные для страницы для этого в angular
есть специальный механизм, его можно испльзовть если заменить атрибуты href на атрибут routerLink,
под капотом ангуляр такж же создаст href но отменет его стандартное поведение и введет динамическую
загрузку данных.

    <nav class="navbar">
      <h1>
        <a [routerLink]="['/']">Angular Routing</a>
      </h1>
      <ul>
        <li class="active">
          <a [routerLink]="['/']">Home</a>  // биндим routerLink для дальнейшего использования.
        </li>
        <li><a [routerLink]="['/posts']">Posts</a></li> // адрес страницы помещаем в массив это необязательно
        <li><a [routerLink]="['/about']">About</a></li> // но так потом проще с этим работать.
        <li><button class="btn">Login</button></li>
        <li><button class="btn">Logout</button></li>
      </ul>
    </nav>
    
    <div class="container">
      <div class="card">
        <router-outlet></router-outlet>
      </div>
    </div>

### Роутинг. Активная ссылка

В angular есть специальная директива routerLinkActive которая помогает отображать активную ссылку, активную станицу,
то есть подствечивать ту страницу на которой сейчас находиться пользователь. Чтобы использоваться эту
директиву нужно прописать атрибут в теге ссылки, пример

      <ul>
        <li routerLinkActive="active"> // вместо class="active"
          <a [routerLink]="['/']">Home</a>
        </li>
        <li routerLinkActive="active"><a [routerLink]="['/posts']">Posts</a></li>
        <li routerLinkActive="active"><a [routerLink]="['/about']">About</a></li>
        <li routerLinkActive="active"><button class="btn">Login</button></li>
        <li routerLinkActive="active"><button class="btn">Logout</button></li>
      </ul>

Но в данном случаем будет иметь проблему, с тем что подтвечиваться будет две ссылки

    <li routerLinkActive="active"><a [routerLink]="['/']">Home</a></li>
    <li routerLinkActive="active"><a [routerLink]="['/about']">About</a></li>

так как в пути
"http://localhost:4200/about" есть две состовляющие  
"http://localhost:4200/" и "/about", чтобы это исправитьв используеться директива
[routerLinkActiveOptions]="{exact: true} она добавляеться как атрибут. 
Мы как бы говорим что данную директиву routerLinkActive стоит использовать только когда
путь полностью совпадает, добавим:

    <li routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">
      <a [routerLink]="['/']">Home</a>
    </li>

### Роутинг. Программная навигация через TypeScript

В данному случаем речь идет о том чтобы при отработке какого либо метода, перемещаться на другую страницу, но не
из шаблона html, как описано выше, а например в простейшем примере, есть кнопка и по событию клика (click)
отрбатывает какая либо функция которая дергает роутер и происходит перемещение на другую страницу. 

html компонента home page

    <h1>Home page</h1>
    
    <div>
      <button class="btn" (click)="goToPostsPage()">Go to Posts</button>
    </div>

ts этого же компонента

    import {Component} from '@angular/core'
    import {Router} from "@angular/router";

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.scss']
    })
    export class HomeComponent {
      constructor(private router: Router) {}
    
      goToPostsPage() {
        this.router.navigate(['/posts']);
      }
    
    }

### Роутинг. Динамические роуты

Здесь речь идет о том, чтобы динамически подставлять нужный параметр в адрес, в зависимости от например id шника
статьи, поста или чего либо другого.
Есть страница с поставми, нужно чтобы при нажатии на каждый элемент поста, совершался переход на конкретный пост,
для этого адрес нужно формироваьт динамически подставляя в него айдишник поста. У нас есть компонент для отображения
всех постов, и есть компонент отображения самого поста.
Дополняем роутинг

    import {NgModule} from "@angular/core";
    import {RouterModule, Routes} from "@angular/router";
    import {HomeComponent} from "./home/home.component";
    import {AboutComponent} from "./about/about.component";
    import {PostsComponent} from "./posts/posts.component";
    import {PostComponent} from "./post/post.component";
    
    // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
    // http://localhost:4200/about -> AboutComponent
    // http://localhost:4200/posts -> PostsComponent
    
    // регистрируем роуты для компонентов
    const routes: Routes = [
      {path: '', component: HomeComponent},
      {path: 'about', component: AboutComponent},
      {path: 'posts', component: PostsComponent},
      {path: 'post/:id', component: PostComponent} // сдесь :id это синтаксис динамического формирования пути
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }

Далее в html компонента постов добавляем динамическое формирование пути с помощью routerLink

    <h1>Posts page</h1>
    
    <button class="btn">
      Show Ids
    </button>
    <button class="btn">
      Show Ids (program)
    </button>
    
    <div class="card" *ngFor="let post of postsService.posts"> 
      <h4>
        <a [routerLink]="['/post',post.id]"> // динамически формируем путь до поста
          <strong>(ID {{post.id}})</strong>
          {{post.title}}
        </a>
      </h4>
    </div>

### Роутинг. Обработка параметрав

Здесь речь пойдет о том чтобы получить параметры из строки запроса, но это не тот вид параметров когда в строке
есть переменная типа ?num=100, а когда запрос имеет вид http://localhost:4200/post/11 и вот 11 это параметр запроса.
В данном случае по этому параметру нужно загрузить данные для поста из бд, как пример. Формирование этого роута
так же идет динамически. То есть мы динамически формируем роут http://localhost:4200/post/11, с параметром 11, см главу
выше, а затем динамически по этому параметру подгружаем данные в сам пост, считывая его.

Чтобы считать этот параметр из url нужно инжектировать в конструктор компонента специальный сервис
ActivatedRoute, затем использовать его в методе NgOnInit() чтобы получить параметры из запроса

    import {Component, OnInit} from '@angular/core'
    import {ActivatedRoute} from "@angular/router";
    
    @Component({
      selector: 'app-post',
      templateUrl: './post.component.html',
      styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit{
    
      constructor(
        private route: ActivatedRoute, // сервис для чтения динамических параметров из url созданных anular
    ) {}
    
      ngOnInit(): void {
        this.route.params.subscribe((params)=>{ // это стрим, получаем параметр в колл бэк функции
          console.log('Params',params);
        })
      }

    }
    
После этого используем самописный сервис postsService чтобы получить инфомацию о постах из импровезированной бд
массива. Код самописного сервиса postsService его нужно, будет так же зарегистрировать в конструкторе и сделать 
импорт в компоненте post где они используеться.


    import {Injectable} from '@angular/core'
    
    export interface Post {
      title: string
      text: string
      id: number
    }
    
    @Injectable({providedIn: 'root'})
    export class PostsService {
      posts: Post[] = [
        {title: 'Post 1', text: 'Sample text for post 1', id: 11},
        {title: 'Post 2', text: 'Sample text for post 2', id: 22},
        {title: 'Post 3', text: 'Sample text for post 3', id: 33},
        {title: 'Post 4', text: 'Sample text for post 4', id: 44},
      ]
    
      getById(id: number) {
        return this.posts.find(p => p.id === id)
      }
    }


Записываем полученные данные в переменную post и отображаем их в шалоне компонента post.

часть ts компонента post
    
      ngOnInit(): void {
        this.route.params.subscribe((params)=>{ // это стрим, получаем параметр в колл бэк функции
          console.log('Params',params);
          this.post = this.postsService.getById(+ params[ 'id']) // получаем id с помощью сервиса postsService
        })
      }

html компонента post

    <div>
      <button class="btn">Back</button>
      <h1>{{post.title}}</h1>
      <p>{{post.text}}</p>
    </div>

Теперь добавим возможность из самого поста переходить на другой пост, для этого добавим кнопку showPost()

    <div>
      <button class="btn">Back</button>
      <h1>{{post.title}}</h1>
      <p>{{post.text}}</p>
      <button class="btn btn-danger" (click)="showPost()">Load 44 post</button> // вот эту кнопку
    </div>
    
реализуем showPost в компоненте поста, в данному случае будем грузить статическое значение 44.
теперь по нажатию на кнопку будет меняться пост на 44.

    //code...
      constructor(
        private route: ActivatedRoute,
        private router: Router,
        private postsService: PostsService
        ) {}
    //code..

    showPost(){
      this.router.navigate(['/posts', 44])
    }

Это показывает что даже находясь в компоненте post из него можно использовать такой же компонент post.

### Роутинг. Работа с query параметрами запроса

Рассмотрим работу с query параметрами запроса ?param=true а так же с параметром ссылкой в запросе #param.
Есть 2 основных пути работаь с параметрами, это в html и в самом ts компонента, в html можно навесить специальные
атрибуты на тег(кнопки) и при клике на нее будут добавляться query параметры в строку запроса, сразу же после этого
будет проверяться строка запроса и выводиться что то исходя из того что в ней есть. 2 способ обрабатывать подобные
клики на кнопку в самом компоненте инжектируя в конструктор компонента специальные сервис, аналог того что уже выше 
рассматривали, который считывает что за параметры есть в строке запроса и опять же делать что то исходя из того
что этот сервис получил.
Начнем с варианта в шаблоне html. Добавим к ts компонента свойство showIds: false, при его изменении будут отображаться
либо не отображаться idшники в шаблоне.

В шаблоне есть кнопка, эта кнопка будет отвечать за показ id шников, если в адресной строке есть параметр showIds: true

    <button
      [routerLink]="['/posts']" // уже знакомая директива
      [queryParams]="{showIds: true}" // эта директива добавляет query параметр в адресную строку
      class="btn"
    >
      Show Ids
    </button>

Теперь при нажатии на кнопку в адресной строке будет появляться query параметр
http://localhost:4200/posts?showIds=true . Теперь нужно принять этот параметр в ts компонента инжектировав
сервис ActivatedRoute. В методе NgOnInit используем этот сервис добавив подписку на изменение query параметров


    import {Component, OnInit} from '@angular/core'
    import {ActivatedRoute} from "@angular/router";
    
    @Component({
      selector: 'app-posts',
      templateUrl: './posts.component.html',
      styleUrls: ['./posts.component.scss']
    })
    export class PostsComponent  implements OnInit{
      showIds = false;
    
      constructor(
        private route: ActivatedRoute,
      ) {}
    
      ngOnInit() {
        this.route.queryParams.subscribe((params)=>{ // получаем query параметры с помощью this.route.queryParams...
          this.showIds = !!params['showIds'] // получаем строку ! инвертируем и 2 ! приводим к булеву значению
        })
      }
    
    }
Теперь при нажатии на кнопку Show Ids, будет  сделан переход на страницу /posts и добавлен параметр showIds: true,
затем компонент инициализируеться и с помощью сериса ActivatedRoute считывает параметры из адресной строки,
отрабатывает метод лайфстаил хуков ngOnInit и меняет переменную showIds на true, angular обновляет шаблон так 
как в нем указано показывать или непоказывать Айдишники в зависимости от этой переменной с помощью структурной
директивы *ngIf в теге strong. 

Теперь почти аналогичную логику создадим но не через добавление директив в html, а через добавление обработки клика
на кнопку Show Ids (program), с помощью метода showIdsProgram()

Сама кнопка

    <button class="btn" (click)="showIdsProgram()">
        Show Ids (program)
    </button>

Теперь в ts компонента инжектируем Router в конструктор с помощью него будут добавляться query параметры в
строку запроса


    import {Component, OnInit} from '@angular/core'
    import {PostsService} from '../posts.service'
    import {ActivatedRoute, Router} from "@angular/router";
    
    @Component({
      selector: 'app-posts',
      templateUrl: './posts.component.html',
      styleUrls: ['./posts.component.scss']
    })
    export class PostsComponent  implements OnInit{
      showIds = false;
    
    
      constructor(
        private route: ActivatedRoute,
        public postsService: PostsService,
        private router: Router
      ) {}
    
      ngOnInit() {
        this.route.queryParams.subscribe((params)=>{
          this.showIds = !!params['showIds'] // получаем строку ! инвертируем и 2 ! приводим к булеву значению
        })
      }
    
      showIdsProgram(){ // при нажатии переходим на /posts и добавляем querry параметр
        this.router.navigate(['/posts'],{queryParams:{showIds:true}}) 
      }
    
    }

Теперь при клике на кнопку "Show Ids (program)" query параметр добавляеться программно в строку запроса, остальная
логика такая же как и в способе с директивами в шаблоне html.


Так же возможно обрабатывать еще один тип параметров fragment в строке запроса который пишеться через #. Опять же есть
два способо это в html шаблоне и програмный через ts компонента.

Способ через html. Добавляем 

    <button
      [routerLink]="['/posts']"
      [queryParams]="{showIds: true}"
      [fragment]="'fragment'" // добавили 
      class="btn"
    >
      Show Ids
    </button>

Теперь адресная строка при нажатии на кнопку будет выглядить вот так http://localhost:4200/posts?showIds=true#fragment

Тоже самое можно сделать и програмно добавив в метод showIdsProgram() в ts компонента свойство
fragment: 'program-fragment'

      showIdsProgram(){
        this.router.navigate(
          ['/posts'],
          {
            queryParams:{showIds:true},
            fragment: 'program-fragment'
          }
    
        )
      }

Теперь можно в ngOnInit отследивать изменения данного параметра program-fragment и что то делать.

      ngOnInit() {
        this.route.queryParams.subscribe((params)=>{
          this.showIds = !!params['showIds'] // получаем строку ! инвертируем и 2 ! приводим к булеву значению
        })
    
        this.route.fragment.subscribe(fragment=>{ // так же подписываемся на observable this.route.fragment.
          console.log('Fragment', fragment) // выводим в консоль его значение
        })
      }

### Роутинг. Вложенные страницы.
Реч сдесь о том, как в один компонент рендерить (вставить) другой компонент, но только не через тег, а
при смене url адреса на такой например http://localhost:4200/about/extra. Например есть компонент about, 
и  внем есть кнопка Extra, при нажатии на нее будет отображаться информация из другово компонента, но смысл
в том что при нажатии на эту кнопку так же будет меняться url на дочерний для about, грубо говоря мы как 
бы вставляем дочерний компонент в родительский при смене url адреса с учетом того что в url содержиться 
часть родительского url.

Чтобы создать такую функциональность нужно в модуле роутов добавить запись о дочернем роуте в родительский.

    import {NgModule} from "@angular/core";
    import {RouterModule, Routes} from "@angular/router";
    import {HomeComponent} from "./home/home.component";
    import {AboutComponent} from "./about/about.component";
    import {PostsComponent} from "./posts/posts.component";
    import {PostComponent} from "./post/post.component";
    import {AboutExtraComponent} from "./about-extra/about-extra.component";
    
    // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
    // http://localhost:4200/about -> AboutComponent
    // http://localhost:4200/posts -> PostsComponent
    // http://localhost:4200/about/extra -> AboutExtraComponent
     
    // регистрируем роуты для компонентов
    const routes: Routes = [
      {path: '', component: HomeComponent},
      {path: 'about', component: AboutComponent, children:[
          {path: 'extra', component: AboutExtraComponent} // добавлен дочерний роут для компонента about
        ] },
      {path: 'posts', component: PostsComponent},
      {path: 'post/:id', component: PostComponent}
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }

Роут для для дочернего компонента about-extra подключили, теперь нужно сделать ссылку для перехода на него
в самом шаблоне родительского компонента about

    <h1>About page</h1>

    <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Amet, magnam!</p>
    
    <p><a [routerLink]="['/about','extra']">Show Extra</a></p> // добавили ссылку на путь до компонента about-extra
    
    <router-outlet></router-outlet> // чтобы ангуляр понимал в какое место в html рендерить дочернить роут extra,
    // для этого нужно вписать сюда тег <router-outlet></router-outlet>

### Роутинг. Редирект и обработка ошибок.

Здесь будем решать вопрос, как отобразить страницу ошибки в том случае если пользоватеьл ввел неправильный url.
Сначала создадим компонент страницы с ошибкой  ng g c error-page --skip-test
Затем добав ее адрес в модуль роутинга. Для этого сначало нужно добавить роутинг для самого компонента страницы
с ошибкой, а чтобы при любом неправлиьном url angular делал редирект на эту страницу, нужно поместить запись вида
{path: '**', redirectTo: '/error' } в конце массива роутов.

    import {NgModule} from "@angular/core";
    import {RouterModule, Routes} from "@angular/router";
    import {HomeComponent} from "./home/home.component";
    import {AboutComponent} from "./about/about.component";
    import {PostsComponent} from "./posts/posts.component";
    import {PostComponent} from "./post/post.component";
    import {AboutExtraComponent} from "./about-extra/about-extra.component";
    import {ErrorPageComponent} from "./error-page/error-page.component";
    
        // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
        // http://localhost:4200/about -> AboutComponent
        // http://localhost:4200/posts -> PostsComponent
        // http://localhost:4200/about/extra -> AboutExtraComponent
        
        // регистрируем роуты для компонентов
        const routes: Routes = [
          {path: '', component: HomeComponent},
          {path: 'about', component: AboutComponent, children:[
              {path: 'extra', component: AboutExtraComponent}
            ] },
          {path: 'posts', component: PostsComponent},
          {path: 'post/:id', component: PostComponent},
          {path: 'error', component: ErrorPageComponent},
          {path: '**', redirectTo: '/error' }
        ];
        
        @NgModule({
          imports: [RouterModule.forRoot(routes)],
          exports: [RouterModule]
        })
        export class AppRoutingModule {
        
        }

### Роутинг. Защита страниц.

Реч о том чтобы запретить посетителю переход на определенные страницы, если у него нет прав доступа.
Для этого создадим в каталоге app фаил auth.guard.ts ,создадим там класс AuthGuard и имплементируем его от 
специального интерфейса CanActivate. Внутри этого класса будет интерфейсный метод canActivate()


    import {ActivatedRouteSnapshot, CanActivate, RouterStateSnapshot} from "@angular/router";
    import {Observable} from "rxjs";

    export class AuthGuard implements CanActivate{

      canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot
      ): Observable<boolean> | Promise<boolean> | boolean {
        return null;
      }

    }

Идея этого класса в том что он принимает два параметра и возвращает определенный boolean параметр,
а точнее принимает роут, и состояние и возвращает вывод открывать или нет для пользователя страницу.

Теперь создадим сервис который будет имулировать работу с авторизацией auth.service.ts 

import {Injectable} from "@angular/core";

    @Injectable({providedIn: 'root'})
    export class AuthService {
      private  isAuth = false 
    
      login(){
        this.isAuth = true; // когда логинимся
      }
    
      logout(){
        this.isAuth = false; // когда разлогиниваемся
      }
    
        // метод эмулирует проверку на беке на аутентификацию
      isAuthenticated(): Promise <boolean> {
        return new Promise(resolve => {
          setTimeout(()=>{
            resolve(this.isAuth)
          },1000)
        })
      }
    }

Теперь инжектируем данный сервис в конструктор основного компонента app.component.ts, чтобы использовать
его в шаблоне с кнопками залогиниться и разлогиниться.

    import {Component} from '@angular/core'
    import {AuthService} from "./auth.servicr";
    
    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
      constructor(public auth: AuthService) {} // инжектируем сервис AuthService
    }

html главного компонента, используем сервис AuthService

    <nav class="navbar">
      <h1>
        <a [routerLink]="['/']">Angular Routing</a>
      </h1>
      <ul>
        <li routerLinkActive="active" [routerLinkActiveOptions]="{exact: true}">
          <a [routerLink]="['/']">Home</a>
        </li>
        <li routerLinkActive="active"><a [routerLink]="['/posts']">Posts</a></li>
        <li routerLinkActive="active"><a [routerLink]="['/about']">About</a></li>
    
        <li routerLinkActive="active"><button class="btn" (click)="auth.login()">Login</button></li>
        <li routerLinkActive="active"><button class="btn" (click)="auth.logout()">Logout</button></li>
      </ul>
    </nav>
    
    <div class="container">
      <div class="card">
        <router-outlet></router-outlet>
      </div>
    </div>


Теперь в классе AuthGuard для того чтобы определитять авторизован ли пользователь нужно обращаться
к сервису AuthService. Для этого необходимо сделать его инжекцию, для этого добавим декоратор
@Injectable({providedIn: 'root'}) и в конструкторе подключим его.
Теперь с помощью подключенного сервиса вернем в методе canActivate булево значение аутентификации с помощью
метода isAuthenticated()

    import {ActivatedRouteSnapshot, CanActivate, Router, RouterStateSnapshot} from "@angular/router";
    import {Observable} from "rxjs";
    import {Injectable} from "@angular/core";
    import {AuthService} from "./auth.servicr";
    
    @Injectable({providedIn: 'root'})
    export class AuthGuard implements CanActivate{
    
      constructor(
        private authService: AuthService,
        private router: Router
      ) {}
    
    
      canActivate( // смысл метода в том чтобы вернуть ответ на вопрос "могу ли я активировать текущий роут"
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot
      ): Observable<boolean> | Promise<boolean> | boolean {
        // @ts-ignore
        return this.authService.isAuthenticated().then( isAuth => { // возвращает Promise будто ответ с бека
          if(isAuth) {
            return true
          } else{
            this.router.navigate(['/'],{ // для редиректа в случае если не аутенфицирован, используем router: Router
              queryParams: {
                auth: false // передаем это в query параметры чтобы в дальнейшем сообщить пользователю что у него нет аутентификации
              }
            })
          }
        })
      }
    
    }

Теперь нужно определить для каких именно роутов будет испльзоваться зашита, включим ее только для страницы с
поставми /posts. Для этого в модуле роутинга пропишем массив классов-гвардов для страницы posts в свойсве canActivate.
То есть мы подключаем массив классов самонаписанных или готовых гвардов, которые будут работать на этой странице.
Вставляя в этом массив названии классов этих гравдов.

    import {NgModule} from "@angular/core";
    import {RouterModule, Routes} from "@angular/router";
    import {HomeComponent} from "./home/home.component";
    import {AboutComponent} from "./about/about.component";
    import {PostsComponent} from "./posts/posts.component";
    import {PostComponent} from "./post/post.component";
    import {AboutExtraComponent} from "./about-extra/about-extra.component";
    import {ErrorPageComponent} from "./error-page/error-page.component";
    import {AuthGuard} from "./auth.guard";
    
    // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
    // http://localhost:4200/about -> AboutComponent
    // http://localhost:4200/posts -> PostsComponent
    // http://localhost:4200/about/extra -> AboutExtraComponent
    
    // регистрируем роуты для компонентов
    const routes: Routes = [
      {path: '', component: HomeComponent},
      {path: 'about', component: AboutComponent, children:[
          {path: 'extra', component: AboutExtraComponent}
        ] },
      {path: 'posts', component: PostsComponent, canActivate: [AuthGuard]}, // вот сдесь массив классов гравдов которые применяються для текущей страницы
      {path: 'post/:id', component: PostComponent},
      {path: 'error', component: ErrorPageComponent},
      {path: '**', redirectTo: '/error' }
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }

Гварды это классы в которых можно реализовать определенный функционал по защите страницы от доступа ненужных
пользователей.

### Роутинг. Защита дочерних страниц.

В гвардах можно указывать что мы защищаем дочернии элементы страниц с помощью интерфейса canActivateChild,
который у казываеться для класса гравада. Выше была глава "Роутинг. Вложенные страницы." в которой говорилось
о вложенных страницах, так вот на эти вложенные страницы так же можн запретить доступ, то есть есть основной 
компонент-старница, он отображаеться, и отдельно в модуле роутинга можно настроить чтобы его дети были недоступны
по какой то причине (настроить гврад). Итак сделаем это:

Нужно сначало добавить интерфейс canActivateChild в сам класс гварда, затем создать метод canActivateChild() 
параметры у нее те же что и у функции от интерфейса CanActivate. А далее внутри метода canActivateChild()
используем метод 

    import {ActivatedRouteSnapshot, CanActivate, CanActivateChild, Router, RouterStateSnapshot} from "@angular/router";
    import {Observable} from "rxjs";
    import {Injectable} from "@angular/core";
    import {AuthService} from "./auth.service";
    
    @Injectable({providedIn: 'root'})
    export class AuthGuard implements CanActivate, CanActivateChild{
    
      constructor(
        private authService: AuthService,
        private router: Router
      ) {}
    
    
      canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot
      ): Observable<boolean> | Promise<boolean> | boolean {
        // @ts-ignore
        return this.authService.isAuthenticated().then( isAuth => {
          if(isAuth) {
            return true
          } else{
            this.router.navigate(['/'],{
              queryParams: {
                auth: false
              }
            })
          }
        })
      }
    
      canActivateChild(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot
      ): Observable<boolean> | Promise<boolean> | boolean {
      return this.canActivate(route, state);
      }
    }

Теперь добавим настроек роутинга, чтобы дочернии элементы страницы about не открывались пока мы не залогинены.


    const routes: Routes = [
    {path: '', component: HomeComponent},
    {
        path: 'about',
        component: AboutComponent,
        children:[
            {path: 'extra', component: AboutExtraComponent}
        ],
        canActivateChild: [AuthGuard] // сюда добавили гуард 
    },
    {path: 'posts', component: PostsComponent, canActivate: [AuthGuard]},
    {path: 'post/:id', component: PostComponent},
    {path: 'error', component: ErrorPageComponent},
    {path: '**', redirectTo: '/error' }
  ];

 
Теперь пути типа http://localhost:4200/about/extra будут недоступны пока пользователь незалогинен, 
то есть работает гвард по доступу к дочерним элементам.

### Роутинг. Резолверы Resolvers.
Резолверы используються для того чтобы загрузить заранее информацию для отображения данных.
Пока резолвер не отработает страница не откроветься. 
Резолверы позволяют вынести логику из компонентов и работать уже в этом слое абстракции
Резолвер это что то схожее с сервисом

Теперь приемер резолвера:
В проекте есть страница/компонент post и в методе ngOnInit() идет проверка url затем по айлишнику в 
url достаються определенные данные для конкретного поста

    import {Component, OnInit} from '@angular/core'
    import {ActivatedRoute, Params, Router} from "@angular/router";
    import {Post, PostsService} from "../posts.service";
    
    @Component({
      selector: 'app-post',
      templateUrl: './post.component.html',
      styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit{
    
      post: Post;
    
      constructor(
        private route: ActivatedRoute,
        private router: Router,
        private postsService: PostsService
    ) {}
    
      ngOnInit(): void {
        this.route.params.subscribe((params)=>{
        this.post = this.postsService.getById(+ params[ 'id']) // получаем сдесь пост
        })
      }
    
      loadPost(){
        this.router.navigate(['/posts', 44])
      }
    
    }

Будем менять с помощью резолвера это загрузку данных для этой страницы, в методе ngOnInit будем использовать
резолвер который будет получать данные поста. Для этого создадим резолвер.
Можно создать отдельную папку для резолверов, а в данном случае просто в корне приложения создадим фаил
post.resolver.ts 

    import {ActivatedRouteSnapshot, Resolve, RouterStateSnapshot} from "@angular/router";
    import {Post, PostsService} from "./posts.service";
    import {delay, Observable, of} from "rxjs";
    import {Injectable} from "@angular/core";
    
    @Injectable({providedIn: "root"}) //  чтобы использовать в резолвере PostsService
    export class PostResolver implements  Resolve<Post>{ // резолвер делаем по интерфейсу Post так резолвер  достает данные постов
      constructor(private postService: PostsService) {
      }
    
        // метод интерфейса Resolve параметры( путь,
      resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Post> | Promise<Post> | Post {
        return of(this.postService.getById(+route.params['id'])) // метод of чтобы возвращать Observable, но можно и просто без него Post возвращать
          .pipe(delay(1500)); // так как возвращаем Observable, то можно использовать методы Observable.

      }
    }

После того как резолвер создан, нужно прописать в роутинг-модуле app.routing.module.ts что для такого пути как post
будет использовать этот resolver, добавив параметр в переменную routes напротв пути post/:id, 
resolve: {post: PostResolver}.

    import {NgModule} from "@angular/core";
    import {RouterModule, Routes} from "@angular/router";
    import {HomeComponent} from "./home/home.component";
    import {AboutComponent} from "./about/about.component";
    import {PostsComponent} from "./posts/posts.component";
    import {PostComponent} from "./post/post.component";
    import {AboutExtraComponent} from "./about-extra/about-extra.component";
    import {ErrorPageComponent} from "./error-page/error-page.component";
    import {AuthGuard} from "./auth.guard";
    import {PostResolver} from "./post.resolver"; // импортируем его сюда
    
    // http://localhost:4200/ -> HomeComponent адрес домашней страницы приложения
    // http://localhost:4200/about -> AboutComponent
    // http://localhost:4200/posts -> PostsComponent
    // http://localhost:4200/about/extra -> AboutExtraComponent
    
    // регистрируем роуты для компонентов
    const routes: Routes = [
      {path: '', component: HomeComponent},
      {
        path: 'about',
        component: AboutComponent,
        children:[
          {path: 'extra', component: AboutExtraComponent}
        ],
        canActivateChild: [AuthGuard]
    
      },
      {path: 'posts', component: PostsComponent, canActivate: [AuthGuard]},
      {
        path: 'post/:id',
        component: PostComponent,
        resolve: {
          post:PostResolver // добавили резолвер
        }
      },
      {path: 'error', component: ErrorPageComponent},
      {path: '**', redirectTo: '/error' }
    ];
    
    @NgModule({
      imports: [RouterModule.forRoot(routes)],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }

Резолвер зарегистрировали теперь нужно использовать его в компоненте post.component.ts, там можно изменить метод
ngOnInit

import {Component, OnInit} from '@angular/core'
import {ActivatedRoute, Router} from "@angular/router";
import {Post, PostsService} from "../posts.service";

    @Component({
      selector: 'app-post',
      templateUrl: './post.component.html',
      styleUrls: ['./post.component.scss']
    })
    export class PostComponent implements OnInit{
    
      post: Post;
    
      constructor(
        private route: ActivatedRoute,
        private router: Router,
        // private postsService: PostsService
    ) {}
    
      ngOnInit(): void {
        // this.post = this.route.snapshot.data['post'];  // snapshot это статически загруженные снимок, он не имеет динамики
        // если есть кнопка на смену url внутри сраницы снепшота, то при ее нажатии url поменяеть, но данные не загрузяться
        // так как snapshot не отследит нажатие кнопки (реч о angularовских наблюдателях) поэтому использовать надо
        // Observable и подписчика, если нежно вывести динамическую инфу, а не просто показать статическую страницус данными.
        this.route.data.subscribe(data=>{
           this.post = data["post"]; // сдесь загрузка идет через резолвер
        })
        // this.route.params.subscribe((params)=>{
        //   console.log('Params',params);
        //   this.post = this.postsService.getById(+ params[ 'id'])
        // })
      }
    
      loadPost(){
        this.router.navigate(['/posts', 44])
      }
    
    }

Резюмируя, Резолверы в Angular используются для предварительной выборки данных: пока пользователь перенаправляется 
с одного маршрута на другой, новая доступная страница уже будет содержать те данные, которые требуется на ней 
отобразить. В некоторых сценариях может потребоваться предварительно загрузить данные для отображения компонента.
В противном случае пришлось бы сначала отображать пустой компонент и затем отправлять запросы к API на получение 
данных.
Резолверы на Angular обычно используются, чтобы предоставить асинхронные данные загружаемому маршруту.
И эти асинхронные данные должны быть отображены в компоненте.

## Модули

### Модули. Что есть в модулях

В файле main.ts запускаеться основоной модуль приложения app.module.ts
main.ts

    import { enableProdMode } from '@angular/core';
    import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
    
    import { AppModule } from './app/app.module';
    import { environment } from './environments/environment';
    
    if (environment.production) {
      enableProdMode();
    }
    
    platformBrowserDynamic().bootstrapModule(AppModule)
      .catch(err => console.error(err));

Мы уже более менее познакомились с декоратором @NgModule который добавляеться к классу модуля:
в массиве declarations регистрирует различные сущности компоненты, директивы, пайпы.
в массиве imports импортируються другие модули, 

* BrowserModule - содержит базовые составляющие angular, наборы
директив пайпов, а так же адаптирует angular под браузер пользователя.
* FormsModule - позволяет работать с двухсторонними биндингами [ngModule] и с формами
* AppRoutingModule - собственный модуля для роутинга

в массиве providers - в неи могут регистрироваться различные сервисы
в массиве bootstrap - в этом поле указан компонент который будет основынм для всего приложения

    import {BrowserModule} from '@angular/platform-browser'
    import {NgModule} from '@angular/core'
    
    import {AppComponent} from './app.component'
    import {FormsModule} from '@angular/forms'
    import {HomePageComponent} from './home-page/home-page.component'
    import {AppRoutingModule} from './app-routing.module'
    import {AboutPageComponent} from './about-page/about-page.component'
    import {AboutExtraPageComponent} from './about-page/about-extra-page/about-extra-page.component'
    import {ColorDirective} from './shared/color.directive'
    import {PageNamePipe} from './shared/page-name.pipe'
    
    @NgModule({
      declarations: [
        AppComponent,
        HomePageComponent,
        AboutPageComponent,
        AboutExtraPageComponent,
        ColorDirective,
        PageNamePipe
      ],
      imports: [
        BrowserModule,
        FormsModule,
        AppRoutingModule,
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule {
    }


Посмотрим на модуль роутинга AppRoutingModule это самописный модуль для роутинга

* массив imports содержит функционал роутов
* массив exports содержит экспортируемые элементы в публичном доступе, доступ к которым будет в тех модулях, куда
будет экспортироваться этот модуль. В данном случае в основном модуле, где импортируеться AppRoutingModule
мы получаем доступ до роутов RouterModule

  
    import {NgModule} from '@angular/core'
    import {RouterModule} from '@angular/router'
    import {HomePageComponent} from './home-page/home-page.component'
    import {AboutPageComponent} from './about-page/about-page.component'
    import {AboutExtraPageComponent} from './about-page/about-extra-page/about-extra-page.component'
    
    @NgModule({
      imports: [RouterModule.forRoot([
        {path: 'about', component: AboutPageComponent, children: [
          {path: 'extra', component: AboutExtraPageComponent}
        ]},
        {path: '', component: HomePageComponent, pathMatch: 'full'}
      ])],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }

### Модули. Создание собственнго модуля
Теперь стоит здача, чтобы в нашем примере блога, каждая страница была отдельным модулем, имееться в виду
что есть страница home,about и вот страница about будет не отдельным компонентом, а отдельным модулем.

Можно сгенерировать модуль с помощью ng cli, но в данном случае сделаем это вручную, добавим фаил в папку about-page
фаил с названием about-page.module.ts, теперь вырежем и вставим туда зависимости которые ранее были в основном модуле

    import {NgModule} from "@angular/core";
    import {AboutPageComponent} from "./about-page.component";
    import {AboutExtraPageComponent} from "./about-extra-page/about-extra-page.component";
    
    @NgModule({
      declarations:[
        AboutPageComponent,
        AboutExtraPageComponent,
      ]
    })
    export class AboutPageModule{
    
    }

Но тут возникает проблема, что для работы этого модуля нужны такие же зависимости как и для основного модуля,
например такая проблема возникает с зависимостью PageNamePipe (это самописный пайп). Если так же прописать эту 
зависимость в declarations этого модуля, то получим ошибку о том что 2 раза пытаемся подлючить одно и то же в 
разных модулях. В таком случае нужно создать еще один модуль, который будет содержать зависимости общие для
нескольких модулей и затем его подключить в гланый модуль. Данный общий модуль с зависимостями будет в папке
shared и называться будет shared.module.ts создаем его

    import {NgModule} from "@angular/core";
    import {ColorDirective} from "./color.directive";
    import {PageNamePipe} from "./page-name.pipe";
    
    @NgModule({
      declarations:[
        ColorDirective, // общая зависимость
        PageNamePipe // общася зависимость
      ]
    })
    export class SharedModule{
      
    }

Теперь в app.module можно удалить все зависимости прописанные в sharedModule. И затем в массиве imports импортировать
SharedModule, так нужно импортировать его в модуле AboutModule. 
Сейчас всеровно вылезут ошибки, так как нужно установить приватные и публичные сущности в модулях.
в sharedModule нужно прописать массив exports, так как поумолчанию все что в этом модуле было подключено в 
declarations будет приватным. exports: [ColorDirective,PageNamePipe].

Теперь разберемся с проблемой что у нас не подключаеться диркетива ngStyle, в html компонента about-page, который
трансформируеться в модуль, дело тут в том, что ngStyle это директива из базового модуля BrowserModule, но
этот модуля импортируеться в основной модуль appModule, а вот в модуле AboutPageModule этот модуль не импортировался
более того 2 раз туда его импортировать нельзя. Есть два решения данного вопроса
1-импортировать в imports AboutPageModule модуль под названием CommonModule, тогда он так же сможет использовать
стандартные элементы директивы Angular.
2-зарегистрировать CommonModule в SharedModule, и не забыть его экспоритровать в массиве exports.

Теперь осталась ошибка с роутинг модулем который подключен в основном модуле, но для aboutPageModule он не подключен.
В данном случае можем так же подключить RouterModule в AboutPageModule в массиве imports, но
использовать RouterModule.forChild, а не RouterModule.forRoute, тогда все будет работать
корректно. Роуты отностящиеся к AboutPageModule необходимо будет соотвественно в него
же и перенести в массив RouterModule.forChild([]), а затем нужно не забыть его порписать
в массиве exports


    import {NgModule} from "@angular/core";
    import {AboutPageComponent} from "./about-page.component";
    import {AboutExtraPageComponent} from "./about-extra-page/about-extra-page.component";
    import {SharedModule} from "../shared/shared.module";
    import {CommonModule} from "@angular/common";
    import {RouterModule} from "@angular/router";
    
    @NgModule({
      declarations:[
        AboutPageComponent,
        AboutExtraPageComponent,
      ],
      imports:[
        CommonModule,
        SharedModule,
        RouterModule.forChild([
          {path: 'about', component: AboutPageComponent, children: [
              {path: 'extra', component: AboutExtraPageComponent}
            ]},
        ])
      ],
        exports:[
        RouterModule
        ]
    })
    export class AboutPageModule{
    
    }


### Модули. Ленивая загрузка модулей
Речь сдесь о том чтобы использовать отложенную загрузку, чтобы не грузить сразу все модули и все элементы при
первом заходе на сайт, а можно сделать отложенную загрузку чтобы только при переходе на about-page грузить
js чанк с кодом для этой страницы.

Чтобы организовать отложенную загрузку нужно в модуле роутинга указать  для роута about (в данном случае),
свойство loadChildren: сюда callback с адресом файла модуля, и далее в then коллбек с название класса
модуля который будет лениво загружен.

    import {NgModule} from '@angular/core'
    import {RouterModule} from '@angular/router'
    import {HomePageComponent} from './home-page/home-page.component'
    
    @NgModule({
      imports: [RouterModule.forRoot([
    
        {path: '', component: HomePageComponent, pathMatch: 'full'},
        { path: 'about',
          loadChildren: () => import('./about-page/about-page.module').then(mod => mod.AboutPageModule)}
      ])],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }
После этого нужно изменить роутинг из самого модуля AboutPageModule, то есть вместо path: 'about' поставиьт
path: '', так как path: 'about' уже есть в основном модуле роутинга и для этого модуля AboutPageModule путь уже
будет корневым.

    imports:[
        CommonModule,
        SharedModule,
        RouterModule.forChild([
            {path: 'about', component: AboutPageComponent, children: [ // изменить на path: ''
                {path: 'extra', component: AboutExtraPageComponent}
            ]},
        ])
    ]

Теперь из самого главнного appModule нужно убрать импорт AboutPageModule, чтобы ангуляр понимал что импортировать чанк 
с jsом  для AboutPageModule  нужно только когда мы переходим на страницу about, а не когда только на стартовую заходим.
Ангуляр понимает это так как в модуле роутинга appRoutingModule уже подписали свойство loadChildren и в нем 
адрес до файла about-page.module.ts и указали что его грузить нужно только при заходе на страницу /about.


### Модули. Изменение стратегии загрузки модулей
Сдесь реч пойдет о том чтобы поменять очередность загрузки модулей, например так чтобы когда пользователь заходит
на основную страницу приложения, сначала загрузились все скрипты необходимые для этой страницы, а затем заранее
подгрузились модули для других страниц, так чтобы когда пользователь будет переходить на другие страницы он не ждал
загрузки скрипта. Для этого в основном модуле роутинга app-routing.module.ts нужно в параметре конфигурации метода
forRoot в объекте передать свойство preloadingStrategy со значением PreloadAllModules.

Тогда все подули будут подгружаться, как написано выше

import {NgModule} from '@angular/core'
import {PreloadAllModules, RouterModule, Routes} from '@angular/router'
import {HomePageComponent} from './home-page/home-page.component'

    const routes: Routes = [
      {path: '', component: HomePageComponent, pathMatch: 'full'},
      { path: 'about',
        loadChildren: () => import('./about-page/about-page.module').then(mod => mod.AboutPageModule)}
    ]
    
    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        preloadingStrategy: PreloadAllModules
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule {
    
    }


## Динамические компоненты

Под динамическими компонентами имееться ввиду когда шаблон компонента создаеться не в html, а в ts коде и затем
этот созданный компонент вставляется в html. Этот подход когда сам компонент и его html могут зависить от каких то 
внешних факторов, и будут создаваться на лету.

### Динамические компоненты.Создание старый способ (прочитать так же важно)
Представим что при нажатии какой либо кнопки должно отображаться попап (модульное окно), вот этот попап и есть компонент
который будем создавать динамически, для начала в шаблоне основного копмонента при клике на кнопку добавим метод
showModal() этот метод в ts основного копонента внутри себя будет создавать компонент. В данном случае учитываем что
шаблон компонента попапа у нас уже есть, и сделан он так же как обычно создаеться какой либло компонент, то есть есть для
компонента есть файлы html - вида, scss и ts.
Шаблон основного компонента ts

    <nav class="navbar">
      <h1>Angular Dynamic components</h1>
    </nav>
    
    <div class="container">
      <div class="card">

        <h1>Some content</h1>
    
        <button class="btn" (click)="showModal()">Show modal</button> // при клике на кнопку выпадает компонент окна
      </div>
    </div>


ts код основного компонента с этим методом:

    import {Component, ComponentFactoryResolver} from '@angular/core'
    import {ModalComponent} from "./modal/modal.component";

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
    
      constructor(private resolver: ComponentFactoryResolver){}
    
      showModal(){
        const modalFactory = this.resolver.resolveComponentFactory(ModalComponent)
      }
    }

Чтобы указать место где в коде должен вставляться этот созданный компонент можно создать директиву, это в целом
хак, но он указан в самой документации. Директива ничего особо делать не будет кроме того что будет помогать находить
ангуляру необходимые компоненты в шаблоне.

    import {Directive, ViewContainerRef} from "@angular/core";
    
    @Directive({
      selector: '[appRef]'
    })
    export class RefDirective {
      constructor(public containerRef: ViewContainerRef) {
    
      }
    }

Теперь добавим в шаблон основного копмонента тег ng-template в котором будем использовать созданную директиву
в данном случае расположение этого тега не играет роли так как мы работаем с компонентом попапа.
    
    <ng-template appRef></ng-template>

Теперь нужно получить доступ до данного компонента попапа модульного окна, для этого используем декоратор 
@ViewChild в ts основного компонента. В этот декорар ранее клали ссылку на тег, но так же можно положить и название
класса директивы, передадим туда нашу директиву RefDirective

    import {Component, ComponentFactoryResolver, ViewChild} from '@angular/core'
    import {ModalComponent} from "./modal/modal.component";
    import {RefDirective} from "./ref.directive";
    
    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
    
      constructor(private resolver: ComponentFactoryResolver){}
    
      @ViewChild(RefDirective,{static: false}) refDir: RefDirective
    
      showModal(){
        const modalFactory = this.resolver.resolveComponentFactory(ModalComponent); // получаем компонент попапа с помощью фабрики
        this.refDir.containerRef.clear(); // получаем ссылку на контайнер куда будем вставлять компонент попапа, чистим этот контайнер, на всякий случай.
        this.refDir.containerRef.createComponent(modalFactory); // вставляем в очищеный контейнер наш комонент попапа
      }
    }
    
Теперь нужно добавить наш компонент попапа в специальный массив entryComponents в основном модуле app.module.ts.
Помимо массива declaration существующего в @NgModule, есть поле entryComponents в котором указывються компоненты
которые используються вприложении но которые нельзя найти в шаблоне. В данном случае ModalComponent не будет использоваться
в виде тега <modal-component> в шаблоне, так как он используеться в качестве динамического компонента.

    import {BrowserModule} from '@angular/platform-browser'
    import {NgModule} from '@angular/core'
    
    import {AppComponent} from './app.component'
    import {FormsModule} from '@angular/forms';
    import { ModalComponent } from './modal/modal.component'
    import {RefDirective} from "./ref.directive";
    
    @NgModule({
      declarations: [
        AppComponent,
        ModalComponent,
        RefDirective
      ],
      imports: [
        BrowserModule,
        FormsModule,
      ],
      providers: [],
      entryComponents: [ModalComponent], // от сюда добавили
      bootstrap: [AppComponent]
    })
    export class AppModule {
    }

Теперь нужно передать необходмые значения параметров попапа в сам компонент попапа, выше для этого все подготовили, а так же
сформировавть действие при нажатии кнопки close в самом попапе, чтобы закрыть его.
для этого в ts основго компонента прописываем следующий код в showModal():
То есть мы из ts основного компонента задаем параметры для динамического компонента, текст внутри него, либо
функцию при нажатии кнопки внутри этого компонента, таким образом все управление находиться вне самого динамического
компонента, потому он и динамический, что его внутренности можно менять из вне.

    import {Component, ComponentFactoryResolver, ViewChild} from '@angular/core'
    import {ModalComponent} from "./modal/modal.component";
    import {RefDirective} from "./ref.directive";

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss']
    })
    export class AppComponent {
    
      constructor(private resolver: ComponentFactoryResolver){}
    
      @ViewChild(RefDirective,{static: false}) refDir: RefDirective
    
      showModal(){
        const modalFactory = this.resolver.resolveComponentFactory(ModalComponent); // получаем компонент попапа с помощью фабрики
        this.refDir.containerRef.clear(); // получаем ссылку на контайнер куда будем вставлять компонент попапа, чистим этот контайнер, на всякий случай.
        const component = this.refDir.containerRef.createComponent(modalFactory); // вставляем в очищеный контейнер наш комонент попапа
    
        component.instance.title = 'Dynamic title'; // обращаемся к полям самого попапа, меняем ему значнение в переменноц title, с ней можно работать так как она @Input
        component.instance.close.subscribe(()=>{ //  подписываемся к переменноц close, это EventEmitter, подписываемся на него для закрытия окна попапа
          this.refDir.containerRef.clear(); // обращаемся к контайнеру для компонента попапа и чистим его, то есть удаляем html попапа из DOM.
        })
      }
    
    }

### Динамические компоненты.Создание новый способ без ComponentFactoryResolver

Теперь попробуем новый способ создания динамических компонентов, так как старый был упразднен, хотя в 13 версии он 
еще работает. Просто существует новое упрощенное Апи для работы с динамическими компонентами.
В первом случае я создам динамический компонент сразу в основном компоненте приложения, чтобы просто понять как это
работает.
Во втором случае динамический компонент будет создаваться с помощью директивы, затем эта дикетва будет применяться
к специальному к какому либо тегу, можно div либо специальному тегу ng-template и отражаться в шаблоне в том месте где
этот тег установлен.
Итак приступим, для первого случая создадим упращенный компонент dyn2-comp.component.ts , который должен отображаться 
как динамический, в данному случае шаблон и стили прописаны уже в компоненте.

    import { Component, OnInit } from '@angular/core';
    
    @Component({
      selector: 'app-dyn-comp',
      template: `<p>dyn-comp works!</p>`,
      styles: ['p{color: red}']
    })
    export class Dyn2CompComponent implements OnInit {
    
      constructor() { }
    
      ngOnInit(): void {
      }
    
    }

Зарегистрируем Dyn2CompComponent в модуле

    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { AppComponent } from './app.component';
    import { HeroJobAdComponent } from './hero-job-ad.component';
    import { HeroProfileComponent } from './hero-profile.component';
    import { AdDirective } from './ad.directive';
    import { AdService } from './ad.service';
    import { AdBannerComponent } from "./ad-banner.component";
    import { DynCompComponent } from './dyn-comp/dyn-comp.component';
    import {DynCompDirective} from "./dyn-comp/dyn-comp.directive";
    import {StyleDirective} from "./dyn-comp/style.directive";
    import {Dyn2CompComponent} from "./dyn2-comp/dyn2-comp.component";

    @NgModule({
      imports: [ BrowserModule ],
      providers: [ AdService ],
      declarations: [
        AppComponent,
        AdBannerComponent,
        HeroJobAdComponent,
        HeroProfileComponent,
        AdDirective,
        DynCompComponent,
        DynCompDirective,
        StyleDirective,
        Dyn2CompComponent
      ],
      bootstrap: [ AppComponent ]
    })
    export class AppModule { }

Теперь внутри основного компонента используем зависимость ViewContainerRef которая позволит, создать контейнер
куда будет помещен компонет, точнее его html, чтобы затем вставить его в DOM.

    import {Component, OnInit, ViewContainerRef} from '@angular/core';
    import {Dyn2CompComponent} from "./dyn2-comp/dyn2-comp.component";


    @Component({
      selector: 'app-root',
      template: `
        <p>Tut about dynamic components</p>
      `
    })
    export class AppComponent implements OnInit {
    
      constructor(private viewContainerRef: ViewContainerRef) {}
    
      ngOnInit() {
        this.viewContainerRef.clear();
        this.viewContainerRef.createComponent(Dyn2CompComponent)
      }
    
    }

Готово, теперь метод this.viewContainerRef.createComponent(Dyn2CompComponent) дообавляет компонент в DOM
динамически, но в данном случае мы не управляем местом куда именно будет вставлен компонент, точнее он просто
будет вставляться как последний html тег в body.

Чтобы управлять местом куда вставиться компонент в html основного компонента необходимо создать директиву, назовем 
ее dyn-comp.directive.ts, название класса ангуляр подскажит как DynCompDirective, далее есть два варината: 
1) сделать так чтобы директива была только для обозначения места, а динамический компонент будет создаваться и вставляться в dom
в коде основного компонента.
2) сделать так чтобы директива могла принимать внутрь себя компонет с помощью декоратора @Input, снаружи из основного 
компонента и затем создавала этот принятый компонент с помощью того же ViewContainerRef как уже сделали выше.

Сделаем второй вариант.

    import {Directive, Input, Type, ViewContainerRef} from "@angular/core";
    
    @Directive({
      selector: '[appDindirective]'
    })
    export class DynCompDirective{
      @Input() appDindirective: Type<any> // Этот тип обозначает что должны принять экземпляр класса или объекта
    
      constructor(private viewContainerRef: ViewContainerRef) {
      }
    
      ngOnInit(): void {
        this.viewContainerRef.createComponent(this.appDindirective);
      }
    }

Так же не забудем задекларировать директиву в модуле в массиве declarations, и затем создадим сам динамический компонент 
внутри ts основного компонента, и передадим его в директиву.

    import {Component, OnInit, ViewContainerRef} from '@angular/core';
    import {DynCompComponent} from "./dyn-comp/dyn-comp.component";


      @Component({
        selector: 'app-root',
        template: `
          <p>Tut about dynamic components</p>
          <ng-template [appDindirective]="dyncomponent"></ng-template>
        `
      })
      export class AppComponent implements OnInit {
      
        dyncomponent = DynCompComponent;
      
        constructor() {}
      
        ngOnInit() {
        }
      
      }


## SEO. Работа с тегами

В данномм случае реч пойдет о работе с тего title, это то что будет написанно во вкладке в браузере. И для SEO 
передаваться. Затем реч будет о том как работать с метатегами.

Как установить тег title. В консрукторе компонента( определенной страницы) добавляем DI типа private title: Title,
затем в теле конструктора пишем title.setTitle('Страница называеться бла бла бла'). Все готово. Чтобы получить title
можно написать title.getTitle(). Если же хотим обратиться не в теле конструктора а где то еще. то нужно добавить
this. чтобы обращаться к свойству класса.

Метатеги. Так же добавялем как di в компонент private meta: Meta, затем пользусь этой переменной можем обращаться к 
различным методам добавления и удаления метатегов. this.meta.addTags([сдесь массив тегов]) можно указывать chatset 
content, httpEquiv и тд.


    import {Component} from '@angular/core';
    import {Meta, Title} from "@angular/platform-browser";
    
    
    @Component({
      selector: 'app-root',
      template: `
        <p>Tut about dynamic components</p>
      `
    })
    export class AppComponent  {
    
      constructor(private title: Title, private meta: Meta) {
    
        title.setTitle('HyeTitle')
    
        this.meta.addTags([
          {name: 'keywords', content: 'angular, google, component'},
          {name: 'description', content: 'this is app component'}
        ])
    
      }
    }

Результат в html страницы

    <head>
        <meta charset="utf-8">
        <title>HyeTitle</title>
        <base href="/">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel="icon" type="image/x-icon" href="favicon.ico">
        <link rel="stylesheet" href="styles.css">
        <meta name="keywords" content="angular, google, component">
        <meta name="description" content="this is app component">
    </head>



### PWA

## PWA модуль, установка и использование.

Чтобы установить PWA для приложения в ангуляре нужно сначало подключить PWA модуль для ангуляра, для
13 версии нужно используя angular cli ввести ng add @angular/pwa@13
Произойдет добавление и апдейт следующих файлов в проекте

    PS D:\Web разработка\учебные проекты\Angular\по курсу Angluar9\angular-basics> ng add @angular/pwa@13
    i Using package manager: npm
    √ Package information loaded.
    
    The package @angular/pwa@13 will be installed and executed.
    Would you like to proceed? Yes
    √ Package successfully installed.
    CREATE ngsw-config.json (631 bytes)
    CREATE src/manifest.webmanifest (1352 bytes)
    CREATE src/assets/icons/icon-128x128.png (1253 bytes)
    CREATE src/assets/icons/icon-144x144.png (1394 bytes)
    CREATE src/assets/icons/icon-152x152.png (1427 bytes)
    CREATE src/assets/icons/icon-192x192.png (1790 bytes)
    CREATE src/assets/icons/icon-384x384.png (3557 bytes)
    CREATE src/assets/icons/icon-512x512.png (5008 bytes)
    CREATE src/assets/icons/icon-72x72.png (792 bytes)
    CREATE src/assets/icons/icon-96x96.png (958 bytes)
    UPDATE angular.json (3424 bytes)
    UPDATE package.json (1119 bytes)
    UPDATE src/app/app.module.ts (945 bytes)
    UPDATE src/index.html (480 bytes)

то есть будут добавлены все основные файлы необходимые для отображения и создания pwa, такие
как
* эконки для pwa
* базовая конфигурация сервис воркера в файле ngsw-config.json
все пааметры для данного файла можно посмотреть в докуменации angular


Насчет билдов, для того чтобы скомпилировать максимально производтьельный билд, нужно обратиться к
angular cli и написать ng build --prod. На выходе будет получена максимально сжатую конфигурацию для
работы ангуляра на проде.

Так же в конфигурацию самого ангуляра - фаил angular.json, добавляеться настройки для сервис воркера
* "scripts": [],
* "serviceWorker": true,
* "ngswConfigPath": "ngsw-config.json" - название созданного сервис воркера

PWA базовое готово, теперь чтобы проверить его, нужно открыть index.html из папки dist в браузере,
но предлварительно нужно установить http-server с помощью команды npm install -g http-server это глобально его 
установит, можно и не глобально для проекта.

Теперь нужно запустить http-server командой  http-server -p 8080

На данный момент сделать с помощью этого всего pwa не получилось. Нужно дополнительно исследовать.


## Typescript для angular

### Typescript для angular. Базовые типы

Здесь описан код с комментариями для базового использования TypeScript с ангуляром

            // базовые типы в ts
        
        let str: string = 'Hello typescript'
        let num: number = 42;
        let isActive: boolean = false;
        
        let strArray: string[] = ['H','e','l'];
        let numArray: Array<number> = [1,2,3];
        
        
        // работа с функциями в ts
        
        function logInfo(name: string,age: number): void{
            console.log(`Info: ${name}, ${age}`);
        }
        
        logInfo('Konstantin',25);
        
        function calc (a: number, b: number | string): number{
            if(typeof b === 'string') b = +b; // сдесь проверяем если b это строка, тогда конвертируем ее в число
            // надо понимать что такая проверка не решит проблему когда b будер вообще строкой типа "пять"
            return a+b;
        }
        
        console.log(calc(2,'5'));
        
        
        // работа с классами в ts
        
        class Server {
            static VERSION = '1.0.3';
            private status: string = 'working';
        
            // вот это вот все аналогично записи
            // public name: string
            // protected ip: number
            //
            // constructor( name: string, ip: number) {
            //     this.name = name;
            //     this.ip = ip;
            // }
            // аналогично записи вот этой
        
            constructor(public name: string, protected ip: number) {
            }
        
            public turnOn() {
                this.status = 'working';
            }
        
            protected turnOff() {
                this.status = 'offline';
            }
        
            getStatus(): string {
                return this.status
            }
        }
        
        const server: Server = new Server('aws',1234);
        
        // Работа с интерфейсами и объектами
        
        interface UserInterface {
            name: string
            age: number
            logInfo2: ()=>void // указываем что объект который будет имплементировать этот интерфейс должен иметь функцию которая ничего не возвращает
            id?: any // необязательное поле так как есть ?
        }
        
        const user: UserInterface = {
            name: 'Vladilen',
            age: 25,
            logInfo2(){
                console.log(this.name+ ' ' +this.age);
            }
        }
        
        // Работа с интерфейсом и классом
        
        interface SayHello{
            sayHello: () => void
        }
        
        class User implements SayHello {
            constructor(private name: string, private age: number) {}
        
            sayHello() {
                console.log(this.name+' Hello');
            }
        
        }
        
        // Generic типы
        // смысл дженериков в том что можно включить в класс заранее неопределенные типы данных, но для которых можно организовать
        // общее поведение.
        const generic: Array<number> = [1,2,3,4,5];
        
        interface Student {
            id: number
            name: string
            age: number
        }
        
        const users: Array<Student> = [
            {id: 1, name: 'K', age: 2},
            {id: 2, name: 'K', age: 3},
            {id: 3, name: 'K', age: 4},
        ]
        
        const users2: Student[] = [ // аналогичное обозначение типа
            {id: 1, name: 'K', age: 2},
            {id: 2, name: 'K', age: 3},
            {id: 3, name: 'K', age: 4},
        ]
        
        // Generic типы создание класса который использует дженерики
        class Observable<T> {
            sayWhat(t: T){
                console.log(t)
            }
        }
        
        const test: Observable<boolean> = new Observable();
        test.sayWhat(true) // выдаст ошибку если положеить в параметр не boolean значение




## RxJs 

### Rxjs, сравнение с обычным js.
Сдесь будет показано как создать одну и ту же функциональность с помощью обычного js и с помощью Rxjs, смысл примера
в том что при нажатии кнопки, будет выводиться через интервал времени имена людей кто старше 18. Есть две кнопки
одна выводит массив людей через js, другая с помощью Rxjs. Для сборки используеться веб пак.

код html.

    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <meta name="viewport"
            content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
      <meta http-equiv="X-UA-Compatible" content="ie=edge">
      <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/css/materialize.min.css">
      <title>RxJS Полный курс</title>
    </head>
    <body style="padding-top: 3rem;">
    
    <div class="container">
      <div id="problem">
        <button class="btn" id="interval">Показать с интервалом</button>
        <button class="btn" id="rxjs">Показать с RxJS</button>
        <div class="result"></div>
        <hr>
      </div>
    </div>
    
    
    </body>
    </html>

Код js с использованием чистого js и Rxjs.

    import {interval} from 'rxjs'
    import {filter,map, take, scan} from "rxjs/operators";
    
    const btn = document.getElementById('interval')
    const rxjsBtn = document.getElementById('rxjs')
    const display = document.querySelector('#problem .result')
    
    const people = [
      {name: 'Vladilen', age: 25},
      {name: 'Elena', age: 17},
      {name: 'Ivan', age: 18},
      {name: 'Igor', age: 14},
      {name: 'Lisa', age: 32},
      {name: 'Irina', age: 23},
      {name: 'Oleg', age: 20}
    ]
    
        // Используем чистый js
    btn.addEventListener('click',()=>{
      console.log('hello');
      btn.disabled = true;
      let i = 0;
      const canDrink = [];
    
      const interval = setInterval(()=>{
        if(people[i])
        {
          if(people[i].age >= 18)
          {
            canDrink.push(people[i].name)
          }
          display.textContent = canDrink.join(' ')
          i++
        }
        else
        {
          clearInterval(interval)
          btn.disabled = false
        }
      },1000)
    
    })
    
        //Используем Rxjs
    rxjsBtn.addEventListener('click',()=>{
    
      rxjsBtn.disabled = true
    
      interval(200)
          .pipe(
              take(people.length), // метод take говорит сколько итерации intreval должно произойти до конца стрима people
              filter(value => people[value].age >= 18), // фильтрует тех кому более 18
              map(value => people[value].name), возвращает новый массив только имен уже с учетом filter
              scan((acc,v) => {return acc.concat(v)},[]) аналог функции reduce в js возвращает результат
          )
          .subscribe(
              res=>{display.textContent = res.join(' ')},  получаем результат выводим его в браузер
              null, // параметр ошибки пока что он не нужен
              ()=>{rxjsBtn.disabled = false} // последний параметр метода subscribe сдесь лежит функция которая сработает при закрытии стрима
    
      )
    })

### Rxjs. Создание различных стримов методы of, for

        import {of,from} from 'rxjs';
        import {scan} from "rxjs/operators";
        
        
        // метод of позволяет создавать стримы из любых данных передаваемых как параметры
        const stream$ =  of(1,2,3,4);
        const stream1$ =  of('hello','hello2');
        
        stream$.subscribe(value => {
            console.log('value: ',value);
            /* выведет следующее
            value:  1
            value:  2
            value:  3
            value:  4
            * */
        })
        
        stream1$.subscribe(value => {
            console.log('value: ',value);
            /* выведет следующее
            value:  hello
            value:  hello2
            * */
        })
        
        // метод from работает с массивами
        const arr$ = from([1,2,3,4]).pipe(
            scan((acc,val) => { return acc.concat(val)},[]) //  с каждой итерацией будет сохранять старые элементы и добаалять новые при выводе
        );
        
        arr$.subscribe(val => console.log("value: ", val));
            /*
            выведет
            value:  [1]
            value:  (2) [1, 2]
            value:  (3) [1, 2, 3]
            value:  (4) [1, 2, 3, 4]
            */

### Rxjs. создание своего стрима Observable 

Сдесь мы создадим сами стрим, и посмотрим как выполняються его методы, в зависимости от того была ли ошибка или нет
или был ли стрим закончен, будет или не будет выполняться дальнейшие сетТаймауты.

        import {Observable} from 'rxjs';

        // создем стрим
        const stream$ = new Observable(observer=>{
            observer.next('First value') // добавляем в стрим значение
        
            // проверяем как стрим выполняет ассинхронные методы, их очередность
            setTimeout(()=>{return observer.next('After 1000ms')},1000)
            setTimeout(()=>{return observer.complete()},1500);
            setTimeout(()=>{return observer.error('Something went wrong')},2000); // 
            setTimeout(()=>{return observer.next('After 1000ms')},3000);
        })
        
        stream$.subscribe(
            (value) => {console.log('Value: ', value)},
            (error) => {console.log(error)},
            ()=>{console.log('Complete')}
            );

        // альтернативная запись обработки стрима
        
        stream$.subscribe({
            next(val){
                console.log(val);
            },
            error(error){
                console.log(error);
            },
            complete(){
                console.log('Complete');
            }
        }

        // еще более удобная запись для subscribe

        stream$.subscribe({
            next: console.log(val),
            error(error){
                console.log(error);
            },
            complete(){
                console.log('Complete');
            }
        }
        /*
        * Если есть error, тогда все сеттаймауты после еррор не выполняться, если есть комплите, то все сеттаймауты после него не 
        * выполняться, комполитом указываем что нужно закончиться стрим.*/

### Rxjs. метод fromEvent()

Данный метод создан для того чтобы обрабатываеть различные события, клики, нажатия клавиш и тд. Метод создает поток
на который можно подписаться и обрабоать нажатие клика. Например при нажатии мышки будет получен поток который можно обработать
с помощью методов pipe, затем подписаться на этот поток и вывести результат пользователю.

Есть кнопка при нажатии на которую просто в консоле показываеться сообщение

    import {fromEvent} from "rxjs";

    fromEvent(document.querySelector('#sayHello'), 'click')
    .subscribe(
        () => {console.log('sayHello')}
    )

    // или вот так, что одно и тоже

    fromEvent(document.querySelector('#sayHello'), 'click')
    .subscribe({
        next: () => {console.log("Helo")},
        error: (error) => {console.log(Error)},
    })

Метод pipe() с какими либо операторами здесь не использовался, так как нет нужны обрабатывать данные в потоке, это просто пример обработки события.

### Rxjs. методы range(),interval(), timer()

Метод interval() создает стрим который через определенный интервал времени можнет совершать определенные действия над 
данными.
    
    // Каждые полсекунды будет выводиться число 1...2...3 и тд до того как пройдет 4 секунды - функция отписки
    const subscribtion = interval(500).subscribe(value => console.log(value))
    setTimeout(()=>{subscribtion.unsubscribe()},4000);

важно понимать что отписываясь от потока, мы его не завершаетм, поток кака считал так и будет дальше считать интервалы

Метод timer()

    // через 2,5 секунды будет получено значение 0, timer() это аналог setTimeOut(), но который выдает стрим.
    timer(2500).subscribe(v=>console.log(v));

метод range()

    //выводит через стрими, набор значений, в данном случае 40,41,42,43,44
    range(40,5).subscribe(console.log);

### Rxjs. класс Subject

Из доки:

Что такое Subject? Subject RxJS — это особый тип Observable, который позволяет передавать значения множеству наблюдателей.
В то время как простые Observable являются одноадресными (каждый подписанный Observer владеет независимым исполнением Observable)

Subject это тот же самый Observable с помощьюкоторого можно создавать стримы, но он позволяет в любой момент эммитить новые событие
То есть при создании стрима из Observable на события можно было только подписываться, т.е. эммитяться события внутри самого класса
Observable, а в подпискеу уже идет получение значения (хотя и там его обработать можно, как душе угодно).

    import {Subject} from "rxjs";
    
    document.addEventListener('click',() => {
    
        const stream$ = new Subject() // берем класс Subject
        stream$.subscribe({next: value => console.log(value)}) // Сначало подписываемся, это важное отличие от пользования обычного Observable
        stream$.next('Hello'); // затем эммити что либо 
        stream$.next('Again Hello') // затем эммити что либо 
        // колличество эммитов не ограничено.
    
    })

        

BehaviorSubject - тот же Subject только то что кладеться в конструктор эммитеться первым.

    import {BehaviorSubject} from "rxjs";

    document.addEventListener('click',() => {

        const stream2$ = new BehaviorSubject('Fist of all') // Работает так же как обычный Subject только у него есть значение по умолчанию, выводимое первым,
        stream2$.subscribe({next: value => console.log(value)})
        stream2$.next('Hello'); 
    })

ReplaySubject - еще одна вариация Subject со своими особенностями в комментариях.

    import { ReplaySubject} from "rxjs";

    document.addEventListener('click',() => {

        //Отличаеться тем что может воспроизвести заэмиченные события до момента подписки, чем тогда от Observable отличаеться зх.
        // Так же  есть буффер, длинна которого задаеться в констуркторе при создании экзепляра класса.
        const stream3$ = new ReplaySubject(1) // значит будет выводиться только последнее заэммиченое значение Hellossss3.

        stream3$.next('Hellossss1');
        stream3$.next('Hellossss2');
        stream3$.next('Hellossss3');

        stream3$.subscribe({next: value => console.log(value)})
    })


### Rxjs. Пример работы методов (операторов) fromEvent, interval, reduce, switchMap, takeWhile , tap, filter, map, take, takeLast, scan

        import {fromEvent, interval, reduce, switchMap, takeWhile} from 'rxjs'
        import {tap, filter, map, take, takeLast, scan} from "rxjs/operators";
        
        const stream$ = interval(500)
            .pipe(
                // данные методы (операторы потока) служат для обработки данных в стриме
                tap(value => console.log('Tap :',value)), // добавляе сторонний эфект, который участвует во всех итерациях, хорошо использовать для дебага
                map(value => value * 3), // перебирает каждый элемент потока
                filter(value => value % 7 === 0), // фильтрует элементы потока по условию
                take(5), // заканчивает стрим после указанного в параметре номера элемента стрима
                takeLast(5), // вернет в метод next только последние 5 значений стрима
                takeWhile(value => value < 7), // аналог оператора take, толькьо стрим будет выводить значения до указанного условия
                scan((acc,val)=> acc + val), // похож на reduce для массивов в js
                reduce((acc,val)=> acc + val) // срабатывает когда стрим завершается (для этого take нужно применить)
        
            )
        
        stream$.subscribe({
            next: value => console.log('Next: ', value),
            complete: () => console.log('Complete')
        })
        
        
        /*Пример с использованием стрима fromEvent, устанавливаем событие по клику, сдесь особенность в том что
        * при клике в стриме мы получем объект event, но он не нужен, а нужно произвести действие по клику, в данном
        * случае для этого используеться метод switchMap, в котором мы подменяем стрим fromEvent на стрим interval
        * и в конечном методе подписки, уже выводим данные из этого стрима обработав их стандарными операторами*/
        fromEvent(document, 'click')
            .pipe(
                switchMap(event => {
                    return interval(1000)
                        .pipe(
                            tap(value => console.log('Tap :',value)),
                            take(5),
                            reduce((acc,val)=> acc + val)
                        )
                })
            )
            .subscribe({
                next: value => console.log('Next2: ',value),
                complete: () => console.log('Complete')
            })
