Sometimes the ``conda install`` commad returns a 500 error::

    cnh-awesome-new-environment) [cnh@node0047 pytorchstyletransfer_satori]$ conda install nbconvert
    Collecting package metadata (current_repodata.json): failed
    CondaHTTPError: HTTP 500 INTERNAL SERVER ERROR for url <https://public.dhe.ibm.com/ibmdl/export/pub/software/server/ibm-ai
    /conda/noarch/current_repodata.json>
    Elapsed: 00:00.490390
    A remote server error occurred when trying to retrieve this URL.
    A 500-type error (e.g. 500, 501, 502, 503, etc.) indicates the server failed to
    fulfill a valid request.  The problem may be spurious, and will resolve itself if you
    try your request again.  If the problem persists, consider notifying the maintainer
    of the remote server.
    
The solution to this is to try again! Sorry....
