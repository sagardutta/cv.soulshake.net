## WOPR

**tl;dr**

    curl -N tty.zone/[0-2]?auto

A markup language for building rich terminal reports, presentations and infographics.
Generate reports using any language. View reports locally or remotely via curl.

**Contributors:**

Yaron Naveh ([@YaronNaveh](http://twitter.com/YaronNaveh))

##Demo##

[pics]


You can view this presentation in your terminal with no installation:

    curl -N tty.zone/[0-2]?auto

If you experience firewall issues replace tty.zone with ec2-23-21-64-152.compute-1.amazonaws.com.

If the presentation is distorted try the verbose url which specifies your screen size:

    curl -N tty.zone/[0-2]?auto\&cols=$(($COLUMNS))\&rows=(($LINES))\&terminal=(($TERM))

These slides auto play which is annoyning for some. You can try this instead to control slides with the Return or Space keys:

    p=0; while true; do curl tty.zone/$((p++)); read; done

You can also use a local viewer rather than curl:

    npm install -g wopr
    curl https://raw.githubusercontent.com/yaronn/wopr/master/test/sample.xml > wopr-sample.xml
    wopr wopr-sample.xml


##Writing your first terminal report##

Here is a simple report with a bar chart:

    <document>
      <page>
        <item col="0" row="0" colSpan="5" rowSpan="4">
          <bar maxHeight="5" data-titles="A,B,C" data-data="2,5,3" />
        </item>
      </page>
    </document>
    
You have 3 options to view this report:

**option 1: POST it to the wopr online viewer**

    curl -N --data '<document><page><item col="0" row="0" colSpan="5" rowSpan="4"><bar maxHeight="5" data-titles="A,B,C" data-data="2,5,3" /></item></page></document>' tty.zone

If you experience firewall issues replace tty.zone with ec2-23-21-64-152.compute-1.amazonaws.com.

**option 2: POST it from external url**

Save the report content in some url (e.g. gist) and then:

    a=$(curl -s https://gist.githubusercontent.com/yaronn/e6eec6d0e7adac63c83f/raw/50aca544d26a32aa189e790635c8679067017948/gistfile1.xml); curl -N --data "$a" tty.zone

(note you need the gist raw url)

If you experience firewall issues replace tty.zone with ec2-23-21-64-152.compute-1.amazonaws.com.

**option 3: via the local viewer**

Save the report xml to report.xml and then:

    npm install -g wopr
    wopr report.xml

Note the local viewer does not send anything online and does not require network.


##Markup Basics#

**Pages**

A document is a set of pages:

    <document>
      <page>
        ...
      </page>
      <page>
        ...
      </page>
    </document>


**Layout**

A page is a 12x12 grid in which you can position different widgets:

    <document>
      <page>
        <item col="0" row="0" colSpan="3" rowSpan="3">
          <bar maxHeight="5" data-titles="A,B,C" data-data="2,5,3" />
        </item>
        <item col="5" row="9" colSpan="1" rowSpan="1">
          <box content="some text" />
        </item>
      </page>
    </document>
    
Here, the bar widget is in the first column and row (0-based indexing) and spans three columns and rows.
The box element is in the same page but in a different position.


**Widgets**
The available widgets are the ones that exist in the [blessed](https://github.com/chjj/blessed) and [blessed-contrib](https://github.com/yaronn/blessed-contrib) projects.
You can infer the xml representation of a javascript widget using a simple convention. So this:

    blessed.widget({ string: "5"
                   , int: 1
                   , intArray: [1,2,3]
                   , stringArray: ["a", "b", "c"]
                   , multiArray: [ [1,2,3], [4,5,6] ]
                   , complexArray: [ {a: 1, b: [1,2] }, {a: 3, b: [3,4]} ]
                   , object: { innerProp: 1, multiArray: [ [1,2], [3,4] ] }
    })

becomes this:
    
  <widget string="5" int="1" intArray="1,2,3" stringArray="a,b,c" object-innerProp="1">
    <multiArray>
      1,2,3
      4,5,6
    </multiArray>
    <object-multiArray>
      1,2
      3,4
    </object-multiArray>
    <complexArray>
      <m a="1" b="1,2" />
      <m a="3" b="3,4" />
    </complexArray>
  </widget>
    
You can also look at the [demo xml](https://raw.githubusercontent.com/yaronn/wopr/master/test/sample.xml) to get more samples.


##Viewing Reports##


Depending on how you use a report, you have a few ways to view it:

*option 1: POST it to the wopr online viewer**

    curl -N --data '<document><page><item col="0" row="0" colSpan="5" rowSpan="4"><bar maxHeight="5" data-titles="A,B,C" data-data="2,5,3" /></item></page></document>' tty.zone

If you experience firewall issues replace tty.zone with ec2-23-21-64-152.compute-1.amazonaws.com.

**option 2: POST it from external url**

Save the report content in some url (e.g. gist) and then:

    a=$(curl -s https://gist.githubusercontent.com/yaronn/e6eec6d0e7adac63c83f/raw/50aca544d26a32aa189e790635c8679067017948/gistfile1.xml); curl -N --data "$a" tty.zone

(note you need the gist raw url)

If you experience firewall issues replace tty.zone with ec2-23-21-64-152.compute-1.amazonaws.com.

**option 3: via the local viewer**

Save the report xml to report.xml and then:

    npm install -g wopr
    wopr report.xml

Note the local viewer does not send anything online and does not require network.


When using the online reports, you might need to adjust the slides size based on your font / resolution or use non-xterm. tty.zone supports the following query params:

    tty.zone?\&cols=$(($COLUMNS))\&rows=(($LINES))\&terminal=(($TERM))
    
    //or use hard coded values
    //curl -N tty.zone?auto\&cols=200\&rows=50
    
(note the backslashs in the query - required in most shells)


*Pages*

When viewing a report with the local viewer you can advance slides with the Return or Space keys.
When using the online viewer you have 2 options:

Option 1 - manually advance slides:

    p=0; while true; do curl --data '<document>...</document>' tty.zone/$((p++)); read; done

Option 2 - auto advance slides:

    curl --data '<document>...</document>' tty.zone/[0-3]?auto

(where 0 is the index of the first slide and 3 of the last slide)


##License##
MIT


## More Information
Created by Yaron Naveh ([twitter](http://twitter.com/YaronNaveh), [blog](http://webservices20.blogspot.com/))