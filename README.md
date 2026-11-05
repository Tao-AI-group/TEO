# Time Event Ontology (TEO)

Time Event Ontology (TEO) is designed to formally represent both structured and unstructured temporal information. It supports semantic representation and reasoning of complex temporal relations of clinical events.

<img width="410" height="287" alt="image" src="https://github.com/user-attachments/assets/499ec92c-9bb7-4d55-9799-3bddc8e6967e" />

Figure 1. Overall Framework of TEO Design and Reasoning Support

## METHOD AND ONTOLOGY DESIGN  
### Meta-level Design 
Under the contemporary paradigm, an ontology comprises classes (concepts), individuals (instances) and properties. A class is a set of entities within a domain that defines a group of individuals that share some common properties. Individuals are instances (concrete examples) of classes. Properties depict the characteristics of a class, stating relationships between individuals (object properties) or from individuals to data values (data properties). For the object property, domain limits the individuals to which the property can be applied, and range limits the individuals that the property may have as its value. The third type of property, annotation properties, are used to associate additional information with ontologies, entities, and axioms. TEO is defined using the OWL. Since TEO aims to model temporal expressions and relations of clinical events, time and event are the two main OWL classes. The class time instant is designed to represent a single time point, and class granularity is used to describe the basic level of the time unit. Class time interval is used to represent a continuous period, combined with class duration to describe its time length. A subclass periodic time interval is devised to describe a periodically or repeatedly occurred time interval. Moreover, a set of object properties are introduced to depict relations, particularly the temporal relations, between classes, and a variety of data properties to describe the data features.  

<img width="468" height="264" alt="image" src="https://github.com/user-attachments/assets/d64532fe-4454-42a3-bdec-1dabd024034d" />
Figure 2. Meta-level Design of TEO

### Class Design for Modeling Temporal Expressions
####	Event
Class event is designed to represent time-oriented medical events, which includes any sort of occurrences, states, procedures or situations that occurs on a timeline[26]. Several subclasses are designed to cover the common clinical events, e.g., clinical intervention, diagnosis and test. More subclasses could be further defined based on the individual use case. Object property hasValidTime links an event individual with a time individual to describe its temporal dimension.
####	Time 
Class time is designated to represent a multiplicity of temporal expressions. It is further subdivided into time instant, time interval and a set of subclasses. 

##### Time Instant and Granularity
Class time instant represents a single segment on the timeline that could be aligned to some unit of a calendar system. It could be a specific day (e.g., “1992-03-05”) or a specific year (e.g., “1992”), etc. Object property hasValidTime links an event individual with a time instant individual. For example, “The subject received the flu vaccine on 26 April 2009”, the <event> (“The subject received the flu vaccine”) hasValidTime <time instant> (“26 April 2009”). 

In natural language, a time instant could be expressed in different formats. For example, “26 April 2009” might appear as “4/26/09” or “04-26-2009”. To achieve normalization, a data property hasNormalizedTime is adopted to connect a time instant individual with a normalized value, which is in the form of “HH:mm:ss YYYY-MM-DD” following the xsd dateTime data type format[33]. For example, the <time instant> (“26 April 2009”) hasNormalizedTime “2009-04-26”. In a real-world setting, temporal information often appears in imprecise or uncertain forms, such as “early August” or “approximately 9 AM”. Data property hasApproximation is used to describe this feature, for which “True” is the value for an “uncertain” instance, while “False” is the value (default) for a “certain” instance, e.g. “at 8:00 AM on Jan 1st, 2017”.

A time instant could be represented at different levels of temporal granularity (such as minute, hour, or day). To describe this feature, a parallel class, granularity, is defined to demonstrate the finest level of granularity. Common temporal granularities, such as <second>, <minute>, <day>, are introduced as its individuals. An instance of time instant is linked with an instance of granularity via the object property hasGranularity, e.g., <time instant> (“8:30 AM”) hasGranularity <granularity> (“Minute”), <time instant> (“Oct 10th, 2017”) hasGranularity <granularity> (“Day”). 

To represent subunits of time instant, such as “morning”, “middle of the week”, a set of subclasses are designed, including instant of the day, instant of the week. These subclasses are further divided into more specific subordinate classes. For instance, “instant of the day” has subclasses of AM, PM, morning, etc. In addition, another subclass time of the age, is designed to record temporal information indicated by the age in years, and a data property hasAgeValue describes its data value. For example, in “At the age of 55, the patient was diagnosed with multiple sclerosis”, <time of the age> (“At the age of 55”) hasAgeValue “55”.

##### Time Interval, Duration and Periodic Time Interval
Class time interval is the region on the timeline that spans more than one single segment. It usually lasts for an amount of time (time length), e.g. “albuterol sulfate aerosol inhalation for 30 minutes (9:30 AM-10:00 AM)”. Class duration is defined to describe the time length of a time interval, by means of the object property hasDuration. For example, in “monitor patient’s heart rate for 72 hours starting from 2014-06-01”, the <time interval> (“72 hours starting from 2014-06-01”) hasDuration <duration> (“72 hours”). Time interval could also have a starting and ending point, which is described by the object properties hasStartTime and hasEndTime respectively. Each of them links to an instance of time instant. For example, in “the cardiac surgery lasted from 9:00 AM to 1:00 PM”, the <time interval> (“from 9:00 AM to 1:00 PM”) hasStartTime <time instant> (“9:00 AM”).

Data property hasNormalizedDuration describes the normalized value of a duration, which is in the format of “0Y0M0W0D0H0m0s”. For example, if the duration is “72 hours”, the value of hasNormalizedDuration is “72H”. Floating numbers are allowed to support precise expression. For example, if a duration lasts for three and a half months, the value of hasNormalizedDuration is “3.5M”. Similarly, if a duration is not certain, the value of data property hasApproximation would be “True”.

It is very common that clinical events recur periodically or regularly, such as chemotherapy, blood glucose monitoring and rehabilitation training. Modified from the HL7 time specification[34], a subclass of time interval, periodic time interval is designed to represent each occurrence of a repeating interval. Further, three object properties are defined: hasRepeatUnit to describe the unit that occurred periodically, hasPeriod to describe the interval between the start time of two units, and hasRepeatUnitInterval to describe the interval between two repeat units of a periodic time interval. In addition, data property hasRepeatTimes is defined to describe the number of units that recurred in a periodic time interval, which is an integer. For example, “The patient received 30 minutes of aerobic exercise every day for 15 days from Feb 3rd, 2005” could be represented in the following RDF triples:
1 <event1> 			rdf:type Event;
2				rdfs:label “The patient received aerobic exercise”;
3				hasValidTime <tPeriodicInterval1>;
4 <duration1>			rdf:type Duration;
5				rdfs:label “30 minutes”;
6				hasNormalizedDuration “30m”;
7 <duration2>			rdf:type Duration;
8				rdfs:label “every day”;
9				hasNormalizedDuration “1D”;
10 <duration3>		rdf:type Duration;
11				rdfs:label “15 days”;
12				hasNormalizedDuration “15D”;
13 < tInstant1>		rdf:type Time Instant;
14				rdfs:label “Feb 3rd, 2005”;
15        hasNormalizedTime “2005-02-03”;
16        hasGranularity <Day>;        
17 <tPeriodicInterval1>	rdf:type Periodic Time Interval; 
18        rdfs:label “30 minutes of aerobic exercise every day for 15 days from Feb 3rd, 2005”;
19        hasRepeatUnit <duration1>;
20        hasPeriod <duration2>;
21        hasDuration<duration3>;
22        hasStartTime <tInstant1>;

### Property Design for Modeling Relations
Properties are deliberately designed for TEO, including object properties (e.g., hasValidTime, hasTemporalRelation, hasGranularity), data properties (e.g., hasApproximation, hasNormalizedTime) and annotation properties (e.g. skos:example). Some of them have been mentioned in the preceding Class Design section. Among them, the object property hasTemporalRelation serves as the parent property to model temporal relations of clinical events. Temporal relations include two main types: qualitative (e.g. angina before headache) and quantitative (e.g. angina 2h before headache). We proposed (1) two sets of temporal relations and (2) temporal relation statement to represent these two types of temporal relations respectively.

#### Two sets of Temporal Relations
We extended Allen’s interval algebra, which is originally designed for temporal relations between intervals, to cover temporal relations between time and time, event and event, and time and event. An explicit temporal order relation relies on comparison of four time point pairs, namely the start and end time from two time intervals/clinical events: (Ts1, Ts2), (Ts1, Te2), (Te1, Ts2), (Te1, Te2), in which Ts1, Te1, Ts2, Te2 represent the start time and end time of <Interval1> and <Interval2> or <Event1> and <Event2> respectively. Allen’s interval algebra defines 13 types of relations, i.e., six pairs of invertible relations (before/after, meets/metBy, overlaps/overlappedBy, starts/startedBy, finishes/finishedBy, during/contains) and one symmetric relation (equal), and transitive axioms are used to hold between these relations. It is applicable to temporal reasoning when both start and end time of two Intervals/Events are known.

However, there are many occasions in which only one time point of each event or interval is known, the strict Allen’s algebra is then not applicable. To address this issue, we designed Basic Time Relations, with which we only need to compare one pair of time points from each Interval/Event. There are 12 types of relations (four pairs of time points and three possible temporal orders) in total, indicating the order of start or end time between two time intervals/events, including startBeforeStart, startEqualEnd, and endBeforeStart. For instance, startBeforeStart means that the start time of the first interval/event is before that of the second interval/event. 

<img width="468" height="192" alt="image" src="https://github.com/user-attachments/assets/88132c71-98a6-42a4-8724-4dbcc5c2ecbb" />
Figure 3. Allen’s Interval Algebra and Basic Time Relations

#### Temporal Relation Statement
Although the Allen’s and Basic Time Relations could represent the qualitative temporal order relations between time/events, it is out of their scope to describe the quantitative information of the time order. For example, in “Patient’s bilirubin is elevated 2 weeks after the second cycle of chemotherapy”, the temporal relation between “Patient’s bilirubin is elevated” and “the second cycle of chemotherapy” is “after” by using Allen’s, but “2 weeks” could not be expressed. To address this issue, TemporalRelationStatement is designed to represent RDF triples, and the object property hasTimeOffset is to specify the duration between events. Currently, hasTimeOffset is limited to the duration from the triple of “<event1> after/before <event2>”. The example sentence could be represented using TemporalRelationStatement and hasTimeOffset in the following RDF triples:
1 <event1> 	rdf:type Event;
2		rdfs:label “Patient’s bilirubin is elevated”;
3		after <event2>;
4 <event2> 	rdf:type Event;
5		rdfs:label “the second cycle of chemotherapy”;
6 <duration1>	rdf:type Duration;
7		rdfs:label “2 weeks”;
8   hasNormalizedDuration “2W”;
9 <state1>	rdf:type TemporalRelationStatement; 
10        	rdf:object <event1>;
11        	rdf:predicate “after”;
12        	rdf:subject <event2>;
13        	hasTimeOffset <duration1>;




