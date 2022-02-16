# sinsakau-2
審査会制作②

//更新内容
スコアの導入  29:public static int ScoreP = 0;
             32:[SerializeField] Text ScoreT;
             44:ScoreT.text = ("Score :"+ScoreP);

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class Player : MonoBehaviour
{
    float pozZ;
    private Rigidbody playerRb;
    public float jumpForce = 5;
    public float gravityModifier = 1;
    public int Junpcount = 2;
    public bool isOnGround = true;
    public bool gameOver=false;
    private Animator playerAnim;
    public ParticleSystem explosionParticle;
    public ParticleSystem dirtParticle;
    public AudioClip jumpSound;
    public AudioClip crashSound;
    private AudioSource playerAudio;
    public static int ScoreP = 0;
    int left = 1;
    int right = 1;
    [SerializeField] Text ScoreT;
    void Start()
    {
        playerRb = GetComponent<Rigidbody>();
        playerAnim = GetComponent<Animator>();
        playerAudio = GetComponent<AudioSource>();
        pozZ = this.gameObject.transform.position.z;
        Physics.gravity *= gravityModifier;
    }

    void Update()
    {
        ScoreT.text = ("Score :"+ScoreP);
        if (Input.GetButtonDown("Jump")  && !gameOver&&Junpcount>0)
            {
            playerRb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isOnGround = false;
            dirtParticle.Stop();
            playerAnim.SetTrigger("Jump_trig");
            playerAudio.PlayOneShot(jumpSound, 1.0f);
            Junpcount--;
        }
        if(Input.GetButtonDown("Fire1") &&left>0)
        {
            left--;
            right++;
            pozZ +=3;
            this.transform.position = new Vector3(this.transform.position.x, this.transform.position.y,pozZ);
        }
        if (Input.GetButtonDown("Fire2") && right > 0)
        {
            right--;
            left++;
            pozZ -= 3;
            this.transform.position = new Vector3(this.transform.position.x, this.transform.position.y, pozZ);
        }
    }
    private void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground")&&!gameOver)
        {
            Junpcount = 1;
            isOnGround = true;
            dirtParticle.Play();

        }
        else if (collision.gameObject.CompareTag("Obstacle")&&!gameOver)
        {
            dirtParticle.Stop();
            explosionParticle.Play();
            gameOver = true;
            playerAnim.SetBool("Death_b", true);
            playerAnim.SetInteger("DeathType_int", 1);
            Debug.Log("GameOver!");
            playerAudio.PlayOneShot(crashSound, 1.0f);

        }else if(collision.gameObject.CompareTag("Score")&&!gameOver)
        {
            ScoreP += 100;
            Destroy(collision.gameObject);
        }
    }
}
