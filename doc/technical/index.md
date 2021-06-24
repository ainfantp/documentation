# Technical documentation

The technical documentation focuses in the technical aspects of the Context Broker Data Visualization enabler, as well as it explains how to develop new features or how specific parts of the enabler work.

## Content
- [Used technologies](#used-technologies)
- [Understanding the code](#understanding-the-code)
  - [How to change the refresh time of the information](#how-to-change-the-refresh-time-of-the-information)
  - [How to change historical data dashboards](#how-to-change-historical-data-dashboards)
  - [How to change notifications dashboard](#how-to-change-notifications-dashboard)
  - [How to change polygon colors](#how-to-change-polygon-colors)
  - [How the information of the map is updated](#how-the-information-of-the-map-is-updated)
  - [Supported types of locations](#supported-types-of-locations)
  - [Supported types of subscriptions](#supported-types-of-subscriptions)
    - [Subscriptions for a specific ID with multiple attributes](#subscriptions-for-a-specific-id-with-multiple-attributes)
    - [Subscriptions for a group of entities of the same type with multiple attributes](#subscriptions-for-a-group-of-entities-of-the-same-type-with-multiple-attributes)
    - [Subscriptions for a group of entities with an idPattern with multiple attributes](#subscriptions-for-a-group-of-entities-with-an-idpattern-with-multiple-attributes)

### Used technologies

The following technologies have been used for the development and the deployment of the Context Broker Data Visualization enabler:

- [Angular](https://angular.io/)
- [OpenStreetMap](https://www.openstreetmap.org/)
- [Leaflet](https://leafletjs.com/)
- [NodeJS](https://nodejs.org/)
- [MongoDB](https://www.mongodb.com/)
- [Docker](https://www.docker.com/)
- [GitHub](https://github.com/)
- [Orion Context Broker](https://github.com/telefonicaid/fiware-orion)
- [FIWARE lab](https://www.fiware.org/developers/fiware-lab/)
- [Markdown](https://www.markdownguide.org/)
- [Python](https://www.python.org/)
- [Elasticsearch](https://www.elastic.co/es/)
- [Kibana](https://www.elastic.co/es/kibana)
- [Apache Nifi](https://nifi.apache.org/)
- [Apache ZooKeeper](https://zookeeper.apache.org/)
- [MySQL](https://www.mysql.com/)


[Top](#technical-documentation)

### Understanding the code

The objective of this section is to explain different technical aspects of the enabler, including new developments, modifying parts of the enabler, etc.

[Top](#technical-documentation)

#### How to change the refresh time of the information

By default, the refresh time of the real-time data in the map is 1 minute. In milliseconds, it is 60000.

This refresh time can be changed, modifying a the value of a variable in the map-dashboard.component.ts.

```typescript
    private intervalRefreshMilliseconds: number = 60000;
```

In this case, the value of the *intervalRefreshMilliseconds* variable (60000) can be replaced by the desired refresh time (in milliseconds).

[Top](#technical-documentation)
### How to change historical data dashboards
The dashboards of the enabler are generated using [Kibana](https://www.elastic.co/es/kibana).

The following steps explain how to change the current kibana dashboards to another dashboard.
 1. Open the **historical-dashboard.component.ts** and **historical-dashboard.component.html** files.
 2. In the **historical-dashboard.component.ts**, there are the different links to each dashboard which will be replaced with the desired URL dashboard and a variable used to switch between dashboards:
```typescript
public firstDashboard: any ="http://link-to-first-dashboard.com";
public secondDashboard: any ="http://link-to-second-dashboard.com";
public dashboardRef: any;
```
 3. The name of the variable should be replaced as well with a descripting name.
 4. It needs a sanitiizer to enable the URL as a secure source and a default dashboard to be displayed:
 ```typescript
    constructor(private sanitizer: DomSanitizer){
    this.dashboardRef = this.sanitizer.bypassSecurityTrustResourceUrl(this.firstDashboard);
    }
 ```
 5. Once all the different links has been changed, we will need to change the switch that acts as a orchestrator to get the proper dashboard link:
 ```typescrypt
 public changeDashboard(dashboard: string): void{
  swtich(dashboard){
    case 'first':
      this.dashboardRef = this.sanitizer.bypassSecurityTrustResourceUrl(this.firstDashboard);
      break;
    case 'second':
      this.dashboardRef = this.sanitizer.bypassSecurityTrustResourceUrl(this.secondDashboard);
      break;
  }
 }
 ```
 6. In the **historical-dashboard.component.html**, add the needed buttons by copying the existing one and change the name and the string passed to the function:
 ```html
<button class="btn text-white mx-1 col-12 col-auto float-right" (click)="changeDashboard('first')">First dashboard</button>
<button class="btn text-white mx-1 col-12 col-auto float-right" (click)="changeDashboard('second')">Second dashboard</button>

```
7. The new dashboards will be displayed if the links are properly set. 

[Top](#technical-documentation)
### How to change notifications dashboard
The dashboards of the enabler are generated using [Kibana](https://www.elastic.co/es/kibana).
The following steps explain how to change the current notifications dashboard:
1. Open the **notifications.component.ts** file.
2. In the **notifications.component.ts**, there is a notifications link to the dashboard:
```typescript
public notifications: any ="http://link-to-notifications-dashboard.com";
public dashboardRef: any;
```
3. As in the previous [How to point](#how-to-change-historical-data-dashboards), it needs a sanitiizer to enable the URL as a secure source and a default dashboard to be displayed:
```typescript
    constructor(private sanitizer: DomSanitizer){
    this.dashboardRef = this.sanitizer.bypassSecurityTrustResourceUrl(this.notifications);
    }
 ```
4. The new notifications dashboard will be changed and it will be shown if the dashboard is compatible with the html iframe tag.

[Top](#technical-documentation)
### How to change polygon colors
If the entity has a polygon object in the location attribute, is possible to change the fill and the stroke color depending on a specific value. 
The following steps explain how to change the colors and the attribute:
1.  Open the **map-dashboard.component.ts** file.
2.  In the variables declaration part, there is an colors object with the name and the hexadecimal code of each color we want: 
```typescript
private colors: any = {low:"#28a746", moderate:"#5894f4",high:"#ffc107",very_high:"#ec7628",extreme:"#dc3546"}
```
3.  Once the colors have been set, the next step is to change the ranges at **getColor** function, placed at **map-dashboard.component.ts** file:
```typescript
    private getColor(index: number): string{
            var color: string;
            if(index < 5){
                color = this.colors.low;
            }else if(index >= 5 && index < 14){
                color = this.colors.moderate;
            }else if(index >= 14 && index < 21){
                color = this.colors.high;
            }else if(index >= 21 && index < 33){
                color = this.colors.very_high;
            }else if(index >= 33){
                color = this.colors.extreme;
            }

            return color;
        }
```
4.  In the **setGeoJSONattributes** function, the desired attribute to define de color can be changed:
```typescript
    private setGeoJSONattributes(model: ModelDto, entity: Entity): string{
            var color: string;
            color = this.getColor(entity.fireWeatherIndex);
            const geoJSON = '{   "fillColor": "'+ color +'",' +
                                '"weight": 2,'+
                                '"opacity": 1,' +
                                '"color": "'+ color +'",' +  //Outline color
                                '"fillOpacity": 0.7' +
                            '}';
            return geoJSON;
        }
```
5.  For the previous step, make sure to have the attribute name declared in the **Entity** interface. Open the **entity.ts** file and add the proper attribute:
```typescript
export interface Entity{
  id?: string;
  type?: string;
  location?:any;
  fireWeatherIndex?:number;
}
```

[Top](#technical-documentation)
#### How the information of the map is updated

In order to optimize the load and update of the information of the map, it is updated based on the following actions:

- For the first load of information (after completing the configuration on the Configuration page), all the sensors are situated on the map. Additionally, the loaded attributes are:
  - ID
  - Type
  - Location
  - Main attribute. More information can be found [here](../user/index.md#configuring-main-attributes).

- Once the initial load of information is completed, the previous attributes are updated every 60 seconds by default. This action allows to visualize sensors whose location is changing during the time (i.e. buses).

- The rest of attributes are updated when the user clicks on a sensor, showing the visualization panel.

- Additionally, when a filter is added, the values for the selected attributes as filters are also automatically updated every 60 seconds by default.

The previous procedure optimizes the load of information and allows to manage high amounts of information in the enabler, avoiding efficiency problems.

[Top](#technical-documentation)

#### Supported types of locations

The supported types of locations of the sensors are [GeoJSON](https://geojson.org/).

This is an example:

```json
"location": {
    "type": "geo:json",
    "value": {
        "type": "Point",
        "coordinates": [-3.712247222222222, 40.423852777777775]
    }
},
```

The **location** attribute of the sensors has to follow the previous schema. The type of the coordinates has to be **Point** or **Polygon**, and its value has to be a **list of two coordinates**. Otherwise, the sensors won't be supported in the enabler (i.e. LineString).

[Top](#technical-documentation)

#### Supported types of subscriptions

With the objective of generating historical data, Cygnus is subscribed to the Context Broker. Then, when the Context Broker receives new data and, if this data matches with the configuration of the subscription, it is sent to Cygnus (and stored in MongoDB).

There are some examples for subscriptions in this [link](https://documenter.getpostman.com/view/513743/RWEgqe8Q?version=latest#5ae8856f-954c-4e6b-a577-e03644bebb70).

In relation with the subscriptions, there are lots of possibilities to configure them. Depending on the IDs of the entities, their attributes, the conditions for the notification, etc.

For the Context Broker Data Visualization enabler, the **supported subscriptions** between Cygnus and the Context Broker are:

##### Subscriptions for a specific ID with multiple attributes

This type of subscription is focused on the entity whose ID is the same as the one indicated in the request. Concretely, in the **id** key.

All the desired attributes to be subscribed for the specific entity, need to be included in the **notification -> attrs list**.

This is the structure of the request for this kind of subscription:

**URL:** <http://localhost:1026/v2/subscriptions/>

```json
{
  "description": "Notify Cygnus the changes of the attributes for the entity ENTITY_ID_1",
  "subject": {
    "entities": [
      {
        "id": "ENTITY_ID_1",
        "type": "TYPE_OF_THE_ENTITY"
      }
    ],
    "condition": {
      "attrs": []
    }
  },
  "notification": {
    "http": {
      "url": "http://cygnus:5051/notify"
    },
    "attrs": [
      "ATTRIBUTE_1",
      "ATTRIBUTE_2",
      "ATTRIBUTE_N"
    ]
  }
}
```

[Top](#technical-documentation)

##### Subscriptions for a group of entities of the same type with multiple attributes

This type of subscription is focused on a **group of entities of the same type**.

All the desired attributes to be subscribed for the specific entity, need to be included in the **notification -> attrs list**.

This is the structure of the request for this kind of subscription:

**URL:** <http://localhost:1026/v2/subscriptions/>

```json
{
  "description": "Notify Cygnus the changes of the attributes for the entities of type TYPE_OF_THE_ENTITIES",
  "subject": {
    "entities": [
      {
        "idPattern": ".*",
        "type": "TYPE_OF_THE_ENTITIES"
      }
    ],
    "condition": {
      "attrs": []
    }
  },
  "notification": {
    "http": {
      "url": "http://cygnus:5051/notify"
    },
    "attrs": [
      "ATTRIBUTE_1",
      "ATTRIBUTE_2",
      "ATTRIBUTE_N"
    ]
  }
}
```

[Top](#technical-documentation)

##### Subscriptions for a group of entities with an idPattern with multiple attributes

This type of subscription is focused on a **group of entities of the same type**, whose **id** matches the regex of the **idPattern** key.

All the desired attributes to be subscribed for the specific entity, need to be included in the **notification -> attrs list**.

This is the structure of the request for this kind of subscription:

**URL:** <http://localhost:1026/v2/subscriptions/>

```json
{
  "description": "Notify Cygnus the changes of the attributes for the entities of type TYPE_OF_THE_ENTITIES which IDs start by PARKING",
  "subject": {
    "entities": [
      {
        "idPattern": "PARKING.*",
        "type": "TYPE_OF_THE_ENTITIES"
      }
    ],
    "condition": {
      "attrs": []
    }
  },
  "notification": {
    "http": {
      "url": "http://cygnus:5051/notify"
    },
    "attrs": [
      "ATTRIBUTE_1",
      "ATTRIBUTE_2",
      "ATTRIBUTE_N"
    ]
  }
}
```

[Top](#technical-documentation)
