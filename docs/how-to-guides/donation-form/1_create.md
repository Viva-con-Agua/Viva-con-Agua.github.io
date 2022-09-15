# How to create an donation form

In order to be able to manage Viva con Agua's online donations, we maintain our own donation form. 
Each form gets its own ID and so it can be assigned to an campaign. This `donation_form_id` also determines other properties.
For example the slider element or the default amount of an donation. 

A `donation_form_id` also refers to a specific company. This allows us to store the authentication information of the payment service providers in our backend.

!!! important "Goal"

    At the end of this guide, you will have a donation form element created via factory service.

!!! important "Prerequisites"

    This guide assumes that you have deployed the [factory]() service or access to the [live factory](https://factory.vivaconagua.org).
    For live deployment you need also access to the [CRM](https://crm.vivaconagua.org). 


## 1) Create an CRM Campaign

 TODO

## 2) Create an donation-form via Factory

- Login into [Factory](https://factory.vivaconagua.org)
- Go to [Formular](https://factory.vivaconagua.org/#/payment-widget) section.
- Open `Formular Anlegen` 


 

## 3) Setting Param

The `setting` param is used to define other "donation modes".

| key | description                        |
|-----|------------------------------------|
| nwt | used for NWT registration payments |
| v2  | new layout (15.09.22)              |

Example:
```
   http://donation-form.vivaconagua.org/#/?donation_form_id=<id>&setting=v2 
```