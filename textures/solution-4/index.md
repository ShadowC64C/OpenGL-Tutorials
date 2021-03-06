```vb
_TITLE "Learning OpenGL" 'giving title to your window
SCREEN _NEWIMAGE(600, 600, 32) 'creating a window of 600x600

'This is our main loop
DO
    _LIMIT 40 'Adding this will prevent high cpu usage.
LOOP

SUB _GL ()
    STATIC glInit
    'Here we'll put our OpenGL commands!
    IF NOT glInit THEN
        glInit = -1
        _glViewport 0, 0, _WIDTH, _HEIGHT 'here _WIDTH() and _HEIGHT() gives the width and height of our window.
        img& = _LOADIMAGE("texture_2.jpg")
        img2& = _NEWIMAGE(_WIDTH(img&), _HEIGHT(img&), 32)
        _PUTIMAGE (0, _HEIGHT(img&))-(_WIDTH(img&), 0), img&, img2&

        STATIC myTex AS LONG, myMask AS LONG 'our texture handle
        _glGenTextures 1, _OFFSET(myTex) 'generate our texture handle
        _glBindTexture _GL_TEXTURE_2D, myTex 'select our texture handle

        DIM m AS _MEM
        m = _MEMIMAGE(img2&) 'we will take data from our image using _MEM

        'giving image data to our texture handle
        _glTexImage2D _GL_TEXTURE_2D, 0, _GL_RGB, _WIDTH(img&), _HEIGHT(img&), 0, _GL_BGRA_EXT, _GL_UNSIGNED_BYTE, m.OFFSET

        _MEMFREE m
        _FREEIMAGE img&
        _FREEIMAGE img2&
        'set out texture filtering
        _glTexParameteri _GL_TEXTURE_2D, _GL_TEXTURE_MAG_FILTER, _GL_LINEAR 'for scaling up
        _glTexParameteri _GL_TEXTURE_2D, _GL_TEXTURE_MIN_FILTER, _GL_NEAREST 'for scaling down

        msk& = _LOADIMAGE("mask.png")
        img2& = _NEWIMAGE(_WIDTH(msk&), _HEIGHT(msk&), 32)
        _PUTIMAGE (0, _HEIGHT)-(_WIDTH, 0), msk&, img2&

        _glGenTextures 1, _OFFSET(myMask)

        _glBindTexture _GL_TEXTURE_2D, myMask 'select our texture handle

        m = _MEMIMAGE(img2&) 'we will take data from our image using _MEM

        'giving image data to our texture handle
        _glTexImage2D _GL_TEXTURE_2D, 0, _GL_RGB, _WIDTH(msk&), _HEIGHT(msk&), 0, _GL_BGRA_EXT, _GL_UNSIGNED_BYTE, m.OFFSET

        _MEMFREE m
        _FREEIMAGE msk&
        _FREEIMAGE img2&

        'set out texture filtering
        _glTexParameteri _GL_TEXTURE_2D, _GL_TEXTURE_MAG_FILTER, _GL_LINEAR 'for scaling up
        _glTexParameteri _GL_TEXTURE_2D, _GL_TEXTURE_MIN_FILTER, _GL_NEAREST 'for scaling down


    END IF

    _glEnable _GL_TEXTURE_2D 'enable texture mapping
    _glEnable _GL_BLEND 'enable blending

    _glClearColor 0, 0, 0, 1 'set color to solid black
    _glClear _GL_COLOR_BUFFER_BIT


    _glBindTexture _GL_TEXTURE_2D, myTex
    _glBegin _GL_QUADS
    _glTexCoord2f 0, 1
    _glVertex2f -1, 1
    _glTexCoord2f 1, 1
    _glVertex2f 1, 1
    _glTexCoord2f 1, 0
    _glVertex2f 1, -1
    _glTexCoord2f 0, 0
    _glVertex2f -1, -1
    _glEnd

    _glBlendFunc _GL_ZERO, _GL_ONE_MINUS_SRC_COLOR 'one liner modification ;)

    _glBindTexture _GL_TEXTURE_2D, 0

    FOR i = -1 TO 1 STEP 0.3
        FOR j = 1 TO -1 STEP -0.3
            _glBegin _GL_QUADS
            _glVertex2f i - 0.1, j + 0.1
            _glVertex2f i + 0.1, j + 0.1
            _glVertex2f i + 0.1, j - 0.1
            _glVertex2f i - 0.1, j - 0.1
            _glEnd
        NEXT
    NEXT

    _glFlush
END SUB
```
