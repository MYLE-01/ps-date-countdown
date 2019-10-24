# Date Countdown
A python script for Homeassistant that counts down the days to birthdays, anniversaries etc

[![hacs_badge](https://img.shields.io/badge/HACS-Default-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)

## How it works
This script creates a sensor that a counts down to the next occurrance of a date, like a birthday or anniversary and gives the number of years as an attribute

Requires `python_script:` to be enabled in you configuration

## Installation
Copy the Python script in to your `/config/python_scripts` directory or install via HACS.

## Script arguments
key | required | type | description
-- | -- | -- | --
`name:` | True | string | Name of the date (eg. John)
`type:` | True | string | Type of date (eg. Birthday)
`date:` | True | string | Date, in format DD/MM/YYYY

## Usage
Each sensor **requires**:

```
name: NAME_OF_DATE
type: TYPE_OF_DATE
date: DD/MM/YYYY_OF DATE
```

examples:

```
name: John
type: birthday
date: 17/08/1971
```

or

```
name: Our wedding
type: anniversary
date: 14/02/1994
```

## Generated sensors
Each sensor is given the following automatically:

```
entity_id: sensor.<type>_<name>
friendly_name: <name> 's <type>
state: <Days to the date from today>
years: <Number of years it will be>
```

So, the two sensors we created above would come out as:

```
sensor.birthday_john
friendly_name: John’s birthday
state: However many days it is until 17th August
years: However old John will be on his next birthday

sensor.anniversary_our_wedding
friendly_name: Our wedding anniversary
state: However many days to 14th February
years: How many years you will have been married on that day
```

## Example configuration.yaml entry
An example automation to create and refresh the above two sensors daily would be:

```yaml
automation:
  - alias: Reminder - Refresh date countdown sensors
    trigger:
      - platform: time
        at: '00:00:01'
      - platform: homeassistant
        event: start
    action:
      - service: python_script.date_countdown
        data:
          name: John
          type: birthday
          date: 17/08/1971
      - service: python_script.date_countdown
        data:
          name: Our wedding
          type: anniversary
          date: 14/02/1994
```

## Example automation
An example automation to remind you of an event 7 days before it occurs would be:

```yaml
automation:
  - alias: Reminder - John's birthday is coming up
    trigger:
      - platform: state
        entity_id: sensor.birthday_john
        to: '7'
    action:
      - wait_template: "{{ states('sensor.time') == '10:00' }}"
      - service: notify.notify
        data:
          message: "John's birthday is only a week away!"
```
## Showing it in lovelace
An example for lovelace
(at this point of time you have to the secondaryinfo-entity-row )

```
  - entity: sensor.anniversary_our_wedding
    secondary_info: ' [[ {entity}.attributes.nextoccur ]]  ( [[ {entity}.attributes.years ]] Years )'
    type: 'custom:secondaryinfo-entity-row'
```
next occur and the year will be displayed in secondaryinfo area of the card
