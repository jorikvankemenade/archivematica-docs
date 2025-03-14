.. _import-metadata:

===============
Import metadata
===============

Archivematica can recognize descriptive and/or rights metadata that is included
with your transfer. You can import metadata by including a directory called
``metadata`` in your transfer. The directory can contain any type of metadata
that you wish to preserve alongside your digital objects. The Process Metadata
Directory Microservice will perform a number of preservation actions on objects
in this directory.

Archivematica supports conventions for importing descriptive metadata and rights
metadata that will transpose the contents of the metadata files into the METS
file. If you are using :ref:`AtoM <atom-setup>` or :ref:`ArchivesSpace
<archivesspace-setup>`, metadata that is transposed to the METS can be passed on
to these access systems for further use.

Metadata that is not able to be transposed to the METS - for example,
preservation metadata created by tool like BitCurator prior to ingest into
Archivematica - will be preserved alongside the materials in the transfer.

Descriptive and/or rights metadata can be added to standard, unzipped, zipped,
and disk image :ref:`transfer types <transfer-types>`.

Metadata in the METS file is searchable in the :ref:`Archival Storage
<archival-storage>` tab.

*On this page:*

* :ref:`Importing descriptive metadata with metadata.csv <metadata.csv>`
    * :ref:`Adding descriptive metadata to individual objects <object-metadata>`
    * :ref:`Adding descriptive metadata to a directory <directory-metadata>`
    * :ref:`Adding non-Dublin Core metadata <non-dc-metadata>`
* :ref:`Importing rights metadata with rights.csv <rights.csv>`
* :ref:`Adding metadata to bags <metadata-bags>`
* :ref:`Importing other types of metadata <other-metadata>`

.. seealso::

   :ref:`Create a transfer with submission documentation <create-submission>`

.. _metadata.csv:

Importing descriptive metadata with metadata.csv
------------------------------------------------

Archivematica natively supports the `Dublin Core Metadata Element Set`_, the
basic 15 Dublin Core metadata elements. Using the metadata.csv method, users can
also include :ref:`non-Dublin Core metadata <non-dc-metadata>`. Archivematica is
able to pass Dublin Core metadata to AtoM or ArchivesSpace, but not non-Dublin
Core metadata.

Descriptive metadata can be applied to individual objects within a transfer, to
directories within the transfer, or both.

.. _object-metadata:

Adding descriptive metadata to individual objects
+++++++++++++++++++++++++++++++++++++++++++++++++

Metadata can be applied to individual objects within a transfer. For example,
you could apply item-level descriptive metadata to an individual image or
multimedia file. The following steps will take you through the basic workflow
for creating a metadata.csv file that will apply metadata to individual objects.

#. Create a transfer directory containing the digital objects you would like to
   preserve (for more information on creating a basic transfer for
   Archivematica, see :ref:`Basic transfers <basic-transfers>`.) As an example,
   the following directory tree displays a basic transfer called
   ``metadataTransfer``. One digital object sits within the top-level directory,
   while another object is nested within a subdirectory.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

   Note that this transfer does not currently contain any metadata.

#. Create a CSV file called metadata.csv. The example below can be used as
   a template for Dublin Core metadata. The filename column must come first and
   the filename path must always start with ``objects/``. Note that if you add
   column headings that are not in the ``dc.element`` format, Archivematica will
   not indicate that the metadata is Dublin Core.

   .. csv-table::
      :file: _csv/individual-metadata.csv
      :header-rows: 1

   Note that empty columns (i.e. ``dc.contributor``) were left in to demonstrate
   the full range of possible Dublin Core values. You do not need to keep empty
   columns.

#. Create a subdirectory called ``metadata`` and place the metadata.csv file
   inside it.::

    metadataTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
       └── metadata.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

Any Dublin Core metadata formatted as in the example above will be transposed to
the METS file. The METS file for the above example includes two descriptive
metadata sections (``<dmdSec>``), one for each file in the transfer. These
contain the Dublin Core metadata parsed from the metadata.csv. Note in the
metadata wrapper (``<mdWrap>``) that they are given an MDTYPE of "DC". If there
had been non-Dublin Core metadata in the metadata.csv, there would be a separate
``<mdWrap>`` for the file with an MDTYPE of "OTHER".

.. literalinclude:: scripts/individual-mets.xml
   :language: xml

.. _directory-metadata:

Adding descriptive metadata to a directory
++++++++++++++++++++++++++++++++++++++++++

Metadata can also be applied to a directory within the transfer. For example,
you could apply directory-level descriptive metadata to folder containing the
pages of a book, where the metadata describes the book itself rather than the
individual pages. The following steps will take you through the basic workflow
for creating a metadata.csv file that will apply metadata to a directory of
objects.

Note that the metadata.csv can contain directory-level metadata, individual
object metadata, or a combination of both.

#. Create a transfer directory containing the digital objects you would like to
   preserve, organized as required within subdirectories (for more information
   on creating a basic transfer for Archivematica, see
   :ref:`Basic transfers <basic-transfers>`.) As an example, the following
   directory tree displays a basic transfer called ``directoryTransfer``. The
   transfer contains two subfolders, which each contain two images.::

    directoryTransfer/
    ├── CoastNews-1964-01-02
    │   ├── page-01.jpg
    │   └── page-02.jpg
    └── CoastNews-1964-01-09
        ├── page-01.jpg
        └── page-02.jpg

   Note that this transfer does not currently contain any metadata.

#. Create a CSV file called metadata.csv. The example below can be used as
   a template for Dublin Core metadata. The filename column must come first and
   the filename path must always start with ``objects/``. In the example below,
   the filename column contains the directory path, instead of a filename path.

   .. csv-table::
      :file: _csv/directory-metadata.csv
      :header-rows: 1

#. Create a subdirectory called ``metadata`` and place the metadata.csv file
   inside it.::

     directoryTransfer/
     ├── CoastNews-1964-01-02
     │   ├── page-01.jpg
     │   └── page-02.jpg
     ├── CoastNews-1964-01-09
     │   ├── page-01.jpg
     │   └── page-02.jpg
     └── metadata
         └── metadata.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

The METS file that results from the above example includes a ``<dmdSec>`` for
each directory, rather than each individual file. These contain the Dublin Core
metadata parsed from the metadata.csv.

.. literalinclude:: scripts/directory-mets.xml
   :language: xml

.. _non-dc-metadata:

Adding non-Dublin Core descriptive metadata
+++++++++++++++++++++++++++++++++++++++++++

You may wish to capture metadata elements that are not supported by the Dublin
Core Metadata Elements Set. As shown in the examples above, Dublin Core metadata
is written to the ``<dmdSec>`` of the METS file with the metadata type indicated
as ``MDTYPE="DC"``. Non-Dublin Core metadata will be written into a separate
``<dmdSec>`` as ``MDTYPE="OTHER"``.

Here is an example of a metadata.csv file containing a mix of Dublin Core and
non-Dublin Core descriptive metadata.

.. csv-table::
   :file: _csv/mixed-metadata.csv
   :header-rows: 1

This results in a METS file containing two ``<dmdSec>`` sections for each directory
- two with ``MDTYPE="DC"`` and two with ``MDTYPE="OTHER"``.

.. literalinclude:: scripts/mixed-metadata-mets.xml
   :language: xml

.. _rights.csv:

Importing rights metadata with rights.csv
-----------------------------------------

Rights information can be associated with objects or directories in a transfer
by creating a rights.csv file, similar to the metadata.csv file used for
descriptive metadata. Archivematica implements :ref:`PREMIS metadata in
Archivematica <premis-template>`, including PREMIS rights, as used here. Rights
metadata added using the rights.csv will be transposed to the METS file.

Rights metadata can be applied to individual objects within a transfer, to
directories within the transfer, or both.

#. Create a transfer directory containing the digital objects you would like to
   preserve. As an example, the following directory tree displays a basic transfer called
   ``rightsTransfer``. One digital object sits within the top-level directory,
   while another object is nested within a subdirectory.::

    rightsTransfer/
    ├── audio
    │   └── bird.mp3
    └── beihai.tif

#. Create a file called rights.csv. The example below can be used as
   a template for PREMIS rights metadata. The filename column must come first
   and the filename path must always start with ``objects/``.

   .. csv-table::
      :file: _csv/rights.csv
      :header-rows: 1

   Note that to enter multiple rights acts for the same basis, you must create
   two separate rows, as with rows 1 and 2 in the example above.

#. Create a subdirectory called ``metadata`` and place the rights.csv file
   inside it.::

    rightsTransfer/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
        └── rights.csv

#. :ref:`Process the transfer <process-transfer>` as usual.

Any PREMIS rights metadata formatted as in the example above will be transposed to
the METS file. The METS file for the above example includes three rights
metadata sections (``<mets:rightsMD>``).

.. literalinclude:: scripts/rights-mets.xml
   :language: xml

.. _metadata-bags:

Adding metadata to bags
-----------------------

Metadata can also be added to transfers packaged in accordance with the Library
of Congress `BagIt`_ specification (zipped or unzipped). Special care needs to
be taken in order to ensure that Archivematica can recognize the metadata files
and process them properly.

#. Create your metadata files. In this example, we will create a metadata.csv to
   add descriptive metadata to the individual objects in the bag.

   .. csv-table::
      :file: _csv/bag-metadata.csv
      :header-rows: 1

   Note that the filename path anticipates the structure of the bag that we will
   create below by including the payload directory (``data``) in the path. If
   your transfer includes subdirectories, those should be reflected in the path
   as well.

#. Assemble your transfer materials. In this example, there are two objects, one
   of which is contained in a subdirectory, as well as the metadata directory
   containing a metadata.csv file.::

    to-be-transferred/
    ├── audio
    │   └── bird.mp3
    ├── beihai.tif
    └── metadata
        └── metadata.csv

#. Create a bag containing the material that you would like to preserve as well
   as the metadata. The digital objects and the metadata directory have been
   added to the payload directory (``data``).::

     bagTest/
     ├── bag-info.txt
     ├── bagit.txt
     ├── data
     │   ├── audio
     │   │   └── bird.mp3
     │   ├── beihai.tif
     │   └── metadata
     │       └── metadata.csv
     ├── manifest-md5.txt
     └── tagmanifest-md5.txt

   Note that when the metadata.csv was created in step 1, above, this structure
   had to be anticipated. Material that you add to a bag will always be inside
   the payload directory, so the filename path must always begin with ``data``.

   The bagging program has also created the required bag files.

#. :ref:`Process the transfer <process-transfer>` using the Unzipped Bag or
   Zipped Bag transfer type.

The METS file in the resulting AIP will include the metadata in the ``<dmdSec>``
as in the non-bagged examples above.

The above method can be used for metadata.csv files that apply metadata to
a directory, for rights.csv files, and for other types of metadata that you wish
to preserve.

.. note::

   Submission documentation can also be added to a bag using the above method.
   The ``submissionDocumentation`` directory should be nested inside the
   metadata directory. See :ref:`Submission documentation <create-submission>`
   for more information.

.. _other-metadata:

Importing other types of metadata
---------------------------------

You can include other types of metadata files in the ``metadata`` directory
other than the metadata.csv and rights.csv files. For example, you may want to
preserve the output of a specialized analysis tool alongside the file. You can
place these files in the metadata directory.::

  rightsTransfer/
  ├── video.mp4
  └── metadata
      ├── qctools.xml.gz
      └── rights.csv

Archivematica will run various preservation tasks on this file, just as it would
on the metadata.csv or rights.csv. In the METS, the file will be listed in the
file section (``<fileSec>``) alongside any other metadata files, under the file
group (``fileGrp``) usage heading ``metadata``.

.. literalinclude:: scripts/metadata-filesec.xml
   :language: xml

:ref:`Back to the top <import-metadata>`

.. _Dublin Core Metadata Element Set: http://www.dublincore.org/specifications/dublin-core/dces/
.. _`BagIt`: https://tools.ietf.org/html/rfc8493
