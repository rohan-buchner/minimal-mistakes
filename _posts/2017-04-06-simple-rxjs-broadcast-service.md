---
layout: single
section-type: post
title: Simple RxJs Broadcast Service
category: development
---

I'm currently working in a system with decoupled components and services. Still being new to Rxjs and Typescript (for Angular 2), I'm not 100% sure of all the utilities available in the RxJs arsenal, but today the need arised to have some form of generic pub/sub or broadcaster helper service. This seemed to do the trick for the time being, but I'd like to improve, or add the ability to only publish to specific subscribers without having to force a property in the payload that we need to use to discriminate who the consumers should be. 

broadcast.service.js
~~~ javascript
import {Injectable} from '@angular/core';
import {BehaviorSubject, Observable} from 'rxjs';

@Injectable()
export class BroadcastService {

  broadcasts: Observable<any>;
  private _broadcasts: BehaviorSubject<any>;

  constructor() {
    this._broadcasts = <BehaviorSubject<any>>new BehaviorSubject({});
    this.broadcasts = this._broadcasts.asObservable();
  }

  send(data): void {
    this._broadcasts.next(Object.assign({}, data));
  }
}
~~~ javascript


Usage:
~~~ javascript
import {Component} from '@angular/core';
import {BroadcastService} from "broadcast.service";

@Component({
  ...
})
export class PublishComponent {

  public broadcastService: BroadcastService;

  constructor(public broadcastService: BroadcastService) {
    this.broadcastService = broadcastService;
  };

  public broadcast(): void {
    this.broadcastService.send({ data: [1, 2, 3], action: 'new' });
  }
}

// somewhere else, eg: another file or component
 broadcastService.broadcasts.subscribe(res => { console.log(res); });
~~~

Hope this helps anyone else who might need to do the same.